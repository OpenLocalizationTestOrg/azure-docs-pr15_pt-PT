<properties
    pageTitle="Indexação ficheiros de multimédia com indexador de multimédia do Microsoft Azure"
    description="Azure multimédia indexador permite-lhe para tornar o conteúdo dos seus ficheiros de multimédia pesquisável e gerar uma transcrição de texto completo para as legendas fechadas e palavras-chave. Este tópico mostra como utilizar indexador de multimédia."
    services="media-services"
    documentationCenter=""
    authors="Asolanki"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="adsolank;juliako;johndeu"/>


# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexação ficheiros de multimédia com indexador de multimédia do Microsoft Azure


Azure multimédia indexador permite-lhe para tornar o conteúdo dos seus ficheiros de multimédia pesquisável e gerar uma transcrição de texto completo para as legendas fechadas e palavras-chave. Pode processar o ficheiro de um multimédia ou vários ficheiros de multimédia numa secção.  

>[AZURE.IMPORTANT] Quando a indexação de conteúdo, certifique-se utilizar os ficheiros de multimédia que tenham voz muito clara (sem música de fundo, ruído, efeitos ou hiss microfone). Alguns exemplos de conteúdo apropriado são: registados reuniões, palestras ou apresentações. O seguinte conteúdo poderão não estar adequado para a indexação: filmes, programas de TV, qualquer com áudio misto e efeitos de som gravado mal conteúdo com o ruído de fundo (hiss).


Uma tarefa de indexação pode gerar os seguintes resultados:

- Fechado ficheiros de legenda nos seguintes formatos: **SAMI**, **TTML**e **WebVTT**.

    Ficheiros de legenda fechada incluam uma etiqueta denominada Recognizability, quais as pontuações de uma tarefa de indexação com base em reconhecível como a voz no vídeo origem está.  Pode utilizar o valor de Recognizability para ficheiros de saída do ecrã da eficiência de utilização. Uma pontuação baixa significa fraco resultados indexação devido a qualidade de áudio.
- Ficheiro de palavra-chave (XML).
- Áudio indexação ficheiro blob (AIB) para utilização com o SQL server.

    Para mais informações, consulte o artigo [Utilizar ficheiros de AIB com indexador de multimédia do Azure e o SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Este tópico mostra como criar indexação para o **índice de um ativo** e **Indexar vários ficheiros**.

Para obter as atualizações mais recentes do indexador de multimédia do Azure, consulte [blogues dos serviços de multimédia](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Utilizar ficheiros de configuração e manifesto para tarefas de indexação

Pode especificar mais detalhes para as suas tarefas indexação utilizando uma configuração de tarefa. Por exemplo, pode especificar os metadados que utilizar para o seu ficheiro de multimédia. Este metadados são utilizado pelo motor de idioma para expandir a sua vocabulário e substancialmente melhora a precisão do reconhecimento de voz.  Também são capaz de especificar os seus ficheiros de saída pretendido.

Também pode processar vários ficheiros de multimédia ao mesmo tempo ao utilizar um ficheiro de manifesto.

Para mais informações, consulte o artigo [Tarefas predefinidos para indexador de multimédia do Azure](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Índice de um ativo

O seguinte método carregamentos de um ficheiro de multimédia como um activo e cria uma tarefa de incluir no índice remissivo elemento.

Tenha em atenção que, se não for especificado nenhum ficheiro de configuração, o ficheiro de multimédia será indexado com todas as predefinições.

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
<!-- __ -->
### <a id="output_files"></a>Ficheiros de saída

Por predefinição, uma tarefa de indexação gera os seguintes ficheiros de saída. Os ficheiros serão armazenados nas primeiro elemento de saída.

Quando há mais de um ficheiro de multimédia de entrada, indexador irá gerar um ficheiro de manifesto saídas de tarefa, com o nome 'JobResult.txt'. Para cada entrada de ficheiro de multimédia, o AIB resultante, SAMI, TTML, WebVTT e ficheiros de palavra-chave, são sequencialmente numerados e com o nome utilizando o "Alias".

Nome do ficheiro | Descrição
----------|------------
__InputFileName.aib__ | Ficheiro de BLOBs indexação áudio. <br /><br /> Ficheiro de áudio Blob indexação (AIB) é um ficheiro binário que pode ser procurado no Microsoft SQL server utilizando a pesquisa de texto completo.  O ficheiro AIB é mais poderoso os ficheiros de legenda simples, porque contém alternativas para cada palavra, permitindo que uma experiência de pesquisa muito mais rica. <br/> <br/>Requer a instalação do Suplemento SQL indexador num máquina executar o Microsoft SQL server 2008 ou posterior. Procurar AIB utilizando o Microsoft SQL procura de texto completo do servidor fornece os resultados de pesquisa mais precisos que procurar os ficheiros de legenda fechada gerados pelo WAMI. Isto acontece porque o AIB contém alternativas word que som semelhante Considerando que os ficheiros de legenda fechada contêm a palavra mais alta de confiança para cada segmento de áudio. Se procurar palavras faladas for de importância upmost, em seguida, é recomendável para utilizar o conjugado AIB com o Microsoft SQL Server.<br/><br/> Para transferir o suplemento, clique em <a href="http://aka.ms/indexersql">Suplementos do Azure multimédia indexador SQL</a>. <br/><br/>Também é possível utilizar outros motores de busca como Apache Lucene/Solr para indexar simplesmente o vídeo com base na legenda fechada e ficheiros XML de palavra-chave, mas isto resultará nos resultados de pesquisa menos precisos.
__InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__ |Ficheiros de legenda (CC) fechados SAMI, TTML e WebVTT formatos.<br/><br/>Podem ser utilizadas para tornar os ficheiros de áudio e vídeos acessível para pessoas portadoras de deficiência auditiva.<br/><br/>Ficheiros de legenda fechados incluam uma etiqueta denominada <b>Recognizability</b> é que as pontuações de uma tarefa de indexação com base em reconhecível como a reprodução de voz na origem de vídeo.  Pode utilizar o valor de <b>Recognizability</b> para ficheiros de saída do ecrã da eficiência de utilização. Uma pontuação baixa significa fraco resultados indexação devido a qualidade de áudio.
__InputFileName.kw.xml<br />InputFileName.info__ |Ficheiros de palavra-chave e informações. <br/><br/>Ficheiro de palavra-chave é um ficheiro XML que contém palavras-chave extraídas do conteúdo de voz, com frequência e informações de deslocamento. <br/><br/>Ficheiro de informações é um ficheiro de texto simples que contém informações granulares sobre cada termo reconhecido. A primeira linha é especial e contém pontuação Recognizability. Cada linha subsequente é uma lista separado por tabulações dos seguintes dados: começar a hora, hora de fim, word/expressão, int. São fornecidas as horas em segundos e a confiança é fornecida como um número entre 1 de 0. <br/><br/>Linha de exemplo: "1,20 1,45 word 0.67" <br/><br/>Estes ficheiros podem ser utilizados para um número de efeitos, tais como, para executar análises de voz, ou expostos motores de pesquisa, tais como o Bing, Google ou Microsoft SharePoint para tornar os ficheiros de multimédia mais detetável, ou até mesmo utilizado para fornecer anúncios mais relevantes.
__JobResult.txt__ |Manifesto de saída, apresentar apenas quando a indexação vários ficheiros, que contém as seguintes informações:<br/><br/><table border="1"><tr><th>Ficheirodeentrada</th><th>Alias</th><th>MediaLength</th><th>Erro</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



Se não todos os ficheiros de suporte de entrada estão indexados com êxito, a tarefa de indexação irá falhar com código de erro 4000. Para mais informações, consulte o artigo [códigos de erro](#error_codes).

## <a name="index-multiple-files"></a>Indexar vários ficheiros

O método que se segue os carregamentos pendentes vários ficheiros de multimédia, como um activo e cria uma tarefa para indexar todos estes ficheiros um lote de.

Um ficheiro de manifesto com a extensão. lst é criado e carregar para o elemento. O ficheiro de manifesto contém a lista de todos os ficheiros de elementos. Para mais informações, consulte o artigo [Tarefas predefinidos para indexador de multimédia do Azure](https://msdn.microsoft.com/library/dn783454.aspx).

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }

### <a name="partially-succeeded-job"></a>Tarefa parcialmente com êxito

Se não todos os ficheiros de suporte de entrada estão indexados com êxito, a tarefa de indexação irá falhar com código de erro 4000. Para mais informações, consulte o artigo [códigos de erro](#error_codes).


As mesmo saídas (como tarefas de bem sucedidas) são geradas. Pode referir-se para o ficheiro manifesto de saída para saber quais os ficheiros entrados são falhou, de acordo com os valores da coluna de erro. Para ficheiros entrados falhou, AIB resultante, SAMI, TTML, WebVTT e palavra-chave não serão gerados ficheiros.

### <a id="preset"></a>Tarefa predefinido para indexador de multimédia do Microsoft Azure

Processamento do indexador de multimédia do Azure pode ser personalizado ao fornecer uma tarefa opcional pré-configurar juntamente com a tarefa.  A seguinte tabela descreve o formato de xml esta configuração.

Nome | Exigir | Descrição
----|----|---
__introdução__ | FALSO | Activo (s) que pretende incluir no índice remissivo.</p><p>Azure multimédia indexador suporta os seguintes formatos de ficheiro de multimédia: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Pode especificar o nome do ficheiro (s) no **nome** ou **lista** atributo do elemento **entrada** (como mostrado abaixo). Se não especificar qual o ficheiro ativo ao índice, o ficheiro principal está selecionado. Se nenhum ficheiro ativo primária for definido, o primeiro ficheiro no elemento entrado é indexado.</p><p>Para especificar explicitamente o nome do ficheiro ativo, efetue:<br />`<input name="TestFile.wmv">`<br /><br />Também pode indexar vários elementos ficheiros ao mesmo tempo (ficheiros até 10). Para fazer isto:<br /><br /><ol class="ordered"><li><p>Criar um ficheiro de texto (ficheiro manifesto) e atribua-lhe uma extensão. lst. </p></li><li><p>Adicione uma lista de todos os nomes de ficheiro de elementos no seu teclado activo a este ficheiro manifesto. </p></li><li><p>Adicione (transferir) thanifest ficheiro elemento.  </p></li><li><p>Especifique o nome do ficheiro manifesto no atributo de lista a entrada de dados.<br />`<input list="input.lst">`</li></ol><br /><br />Nota: Se adicionar mais de 10 ficheiros para o ficheiro de manifesto, a tarefa de indexação irá falhar com o código de erro de 2006.
__metadados__ | FALSO | Metadados para os ficheiros de elementos especificado utilizados para vocabulário adaptação.  É útil para preparar indexador reconhecer as palavras vocabulário não padrão, tais como nomes próprios.<br />`<metadata key="..." value="..."/>` <br /><br />Pode fornecer __valores__ para predefinidas __teclas__. Atualmente são suportadas as teclas seguintes:<br /><br />"título" e "Descrição" - utilizado para adaptação vocabulário para optimizar o idioma do modelo para a tarefa e melhore a precisão do reconhecimento de voz.  Os valores de propagar procuras na Internet para encontrar documentos relevantes contextually texto, utilizando os conteúdos para aumentar o dicionário interno para a duração da sua tarefa de indexação.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__funcionalidades__ <br /><br /> Adicionado na versão 1.2. Atualmente, a funcionalidade de suportados apenas é o reconhecimento de voz ("recuperação automática").| FALSO | A funcionalidade de reconhecimento de voz tem as seguintes chaves de definições:<table><tr><th><p>Chave</p></th>     <th><p>Descrição</p></th><th><p>Valor de exemplo</p></th></tr><tr><td><p>Idioma</p></td><td><p>A linguagem natural para será reconhecido no ficheiro de multimédia.</p></td><td><p>Inglês, espanhol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>uma lista separados por ponto e vírgula dos formatos de legenda de exportação pretendido (se existir)</p></td><td><p>ttml; sami; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Um sinalizador booleano especificando se ou não um ficheiro AIB é necessário (para utilização com o SQL Server e o cliente indexador IFilter).  Para mais informações, consulte o artigo <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Utilizar ficheiros de AIB com indexador de multimédia do Azure e o SQL Server</a>.</p></td><td><p>VERDADEIRO; FALSO</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Um sinalizador booleano especificando se ou não é necessário um ficheiro XML de palavra-chave.</p></td><td><p>VERDADEIRO; FALSO. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Um sinalizador booleano especificar se pretende ou não forçar legendas completos (independentemente do nível de confiança).  </p><p>Predefinição é FALSO, caso em palavras e expressões que têm um menor do que o nível de confiança de 50% são omitidos saídas de legenda final e substituídos nas reticências ("...").  Nas reticências são úteis para auditoria e controlo da qualidade de legenda.</p></td><td><p>VERDADEIRO; FALSO. </p></td></tr></table>

### <a id="error_codes"></a>Códigos de erro

No caso de um erro, deve relatar indexador de multimédia do Azure trás de um dos seguintes códigos de erro:

Código | Nome | Motivos possíveis
-----|------|------------------
2000 | Configuração inválida | Configuração inválida
2001 | Elementos de entrada inválidos | Em falta elementos de entrada ou elementos vazio.
2002 | Manifesto inválido | Manifesto está vazio ou manifesto contém itens inválidos.
2003 | Ocorreu uma falha ao transferir o ficheiro de multimédia | URL inválido no ficheiro de manifesto.
2004 | Protocolo não suportado | Protocolo do URL de multimédia não é suportado.
2005 | Tipo de ficheiro não suportados | Tipo de ficheiro de entrada de multimédia não é suportado.
de 2006 | Demasiadas ficheiros de entrada | Existem mais de 10 ficheiros no manifesto da entrada.
3000 | Ocorreu uma falha ao descodificar o ficheiro de multimédia | Codec de multimédia não suportadas <br/>ou<br/> Ficheiro de multimédia danificado <br/>ou<br/> Nenhuma sequência de áudio na entrada de multimédia.
4000 | Indexação do lote parcialmente foi concluída com êxito | Alguns dos ficheiros de multimédia entrada são Ocorreu uma falha ao ser indexadas. Para mais informações, consulte o artigo <a href="#output_files">ficheiros de saída</a>.
outros | Erros internos | Contacte a equipa de suporte. indexer@microsoft.com


## <a id="supported_languages"></a>Idiomas suportados

Atualmente, são suportados os idiomas inglês e espanhol. Para obter mais informações, consulte [a mensagem de blogue de lançamento v 1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Ligações relacionadas

[Descrição geral de análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Utilizar ficheiros AIB com indexador de multimédia do Microsoft Azure e o SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indexação ficheiros de multimédia com a pré-visualização do indexador 2 de multimédia do Microsoft Azure](media-services-process-content-with-indexer2.md)
