<properties
    pageTitle="Analisar os seus ficheiros de multimédia através do portal Azure | Microsoft Azure"
    description="Este tópico explica como os ficheiros de multimédia com processadores de multimédia de análise de multimédia (MPs) utilizando o portal Azure do processo."
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


# <a name="analyze-your-media-using-the-azure-portal"></a>Analisar os seus ficheiros de multimédia através do portal Azure

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="overview"></a>Descrição geral

Análise de serviços de multimédia Azure é uma coleção de voz e de visão componentes (na escala enterprise, conformidade, segurança e alcance global) que tornam mais fácil para organizações e empresas para obter informações acionáveis a partir dos seus ficheiros de vídeos. Para mais detalhada descrição geral da análise de serviços de multimédia do Azure consulte [Este](media-services-analytics-overview.md) tópico. 

Este tópico explica como os ficheiros de multimédia com processadores de multimédia de análise de multimédia (MPs) utilizando o portal Azure do processo. Multimédia Analytics MPs produzir MP4 ficheiros ou ficheiros JSON. Se um processador de multimédia produzidos um ficheiro de MP4, gradualmente pode transferir o ficheiro. Se um processador de multimédia produzidos um ficheiro JSON, pode transferir o ficheiro a partir do armazenamento de Blobs do Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Escolha um ativo que pretende analisar 
 
1. No [portal do Azure](https://portal.azure.com/), selecione a sua conta dos serviços de multimédia do Azure.
2. Na janela **Definições** , selecione **recursos**.  
.
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)

2. Selecione os elementos que pretender para analisar e prima o botão **Analisar** .
        
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)

3. Na janela de **elementos de multimédia de processo com a análise de multimédia** , selecione o processador. 

    O resto do artigo explica porquê e como utilizar cada processador. 
   
4. Prima **Criar** para iniciar uma tarefa.

## <a name="azure-media-indexer"></a>Indexador de multimédia do Microsoft Azure

O processador de multimédia do **Azure multimédia indexador** permite-lhe tornar os ficheiros de multimédia e conteúdo pesquisável, bem como gerar fechadas faixas legendas. Esta secção fornece alguns detalhes sobre as opções que pode especificar para este MP.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Idioma

A linguagem natural para será reconhecido no ficheiro de multimédia. Por exemplo, inglês ou espanhol. 

### <a name="captions"></a>Legendas

Pode escolher um formato da legenda que será gerado a partir do seu conteúdo. Uma tarefa de indexação pode gerar legenda fechada ficheiros nos seguintes formatos:  

- **SAMI**
- **TTML**
- **WebVTT**

Fechada legenda (CC) ficheiros nestes formatos podem ser utilizados para tornar os ficheiros de áudio e vídeos acessível para pessoas portadoras de deficiência auditiva.

### <a name="aib-file"></a>Ficheiro AIB

Selecione esta opção se pretender para gerar o ficheiro de áudio Blob de índice remissivo para utilização com o servidor IFilter personalizado do SQL. Para mais informações, consulte o artigo [neste](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blogue.

### <a name="keywords"></a>Palavras-chave

Selecione esta opção se pretender para gerar um ficheiro XML de palavras-chave. Este ficheiro contém palavras-chave extraídas do conteúdo de voz, com frequência e informações de deslocamento.

### <a name="job-name"></a>Nome da tarefa

Um nome amigável permite-lhe identificar a tarefa. [Este](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de exportação

Um nome amigável permite-lhe identificar o conteúdo de saída. 

## <a name="azure-media-hyperlapse"></a>Hyperlapse de multimédia do Microsoft Azure

Azure multimédia Hyperlapse é uma MP que cria suaves vídeos tempo decorrido do conteúdo da primeira pessoa ou da câmara de ação.  Para mais informações, consulte o artigo [neste](media-services-hyperlapse-content.md) tópico. Esta secção fornece alguns detalhes sobre as opções que pode especificar para este MP.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Velocidade 

Especifique a velocidade com as quais acelerar o vídeo introdução. O resultado é uma representação estabilizada e tempo decorrido do vídeo de entrada.

### <a name="job-name"></a>Nome da tarefa

Um nome amigável permite-lhe identificar a tarefa. [Este](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de exportação

Um nome amigável permite-lhe identificar o conteúdo de saída. 

## <a name="azure-media-face-detector"></a>Multimédia do Microsoft Azure nominal Detector

O processador de multimédia do **Azure multimédia nominal Detector** (programa mínimo) permite-lhe contar, controlar movimentos e até mesmo avaliar participação da audiência e resposta através de expressões faciais. Este serviço contém duas funcionalidades: 

- **Deteção de nominal**

    Nominal deteção de documentos localiza e controla faces humanos dentro de um vídeo. Várias faces podem ser detetados e subsequentemente ser registados à medida que deslocar-se, com os metadados de hora e a localização devolvido num ficheiro JSON. Durante o controlo, tentará para dar um ID consistente para a mesma face enquanto a pessoa é mover-se no ecrã, mesmo são obstruídas ou brevemente sair da moldura.

    >[AZURE.NOTE]Este serviços não efetua reconhecimento facial. Um indivíduo que deixa a moldura ou torna-se obstruída para muito tempo será dada um novo ID quando devolverem.

- **Deteção de emoções**
    
    Emoções deteção é um componente opcional de processador de multimédia de detecção nominal que devolve análise no vários afectivos atributos da faces detectadas, incluindo felicidade, sadness, receio, anger e muito mais. 

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modo de detecção

Um dos seguintes modos de pode ser utilizado pelo processador:

- Deteção de nominal
- por deteção de emoções nominal
- Deteção de agregação emoções

### <a name="job-name"></a>Nome da tarefa

Um nome amigável permite-lhe identificar a tarefa. [Este](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de exportação

Um nome amigável permite-lhe identificar o conteúdo de saída. 

## <a name="azure-media-motion-detector"></a>Multimédia do Microsoft Azure Detector de movimento

O processador de multimédia do **Azure multimédia movimento Detector** (programa mínimo) permite-lhe identificar eficientemente secções de interesse dentro de um vídeo caso contrário longo e decorrer. Deteção de movimento pode ser utilizada no imagens estáticas câmara para identificar secções do vídeo onde movimento ocorre. Gera um ficheiro JSON que contenha um metadados com selos de tempo e o delimitadora região onde o evento ocorreu.

Esta tecnologia orientadas para feeds de vídeo de segurança, é possível categorizar movimento em eventos relevantes e falsos como sombreados e alterações de iluminação. Esta opção permite-lhe gerar alertas de segurança da câmara feeds sem a ser spammed com infinito eventos irrelevantes, ser capaz de extrair minutos de interesse de vídeos de controlo demasiado longo.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Miniaturas de vídeo de multimédia do Microsoft Azure

Este processador pode ajudar a criar resumos dos vídeos mais longos selecionando automaticamente fragmentos interessantes o vídeo de origem. Isto é útil quando quiser fornecer uma descrição geral do que esperar num vídeo por extenso. Para informações e exemplos detalhados, consulte o artigo [Utilizar Azure multimédia vídeo miniaturas para criar um resumo de vídeo](media-services-video-summarization.md)

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome da tarefa

Um nome amigável permite-lhe identificar a tarefa. [Este](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de exportação

Um nome amigável permite-lhe identificar o conteúdo de saída. 


##<a name="next-steps"></a>Próximos passos

Vista dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


