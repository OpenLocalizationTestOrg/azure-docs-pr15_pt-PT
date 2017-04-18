<properties 
    pageTitle="Criar fluxos de trabalho de codificação avançados estruturador do fluxo de trabalho | Microsoft Azure" 
    description="Saiba mais sobre como criar fluxos de trabalho de codificação avançados estruturador do fluxo de trabalho." 
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
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;johndeu;anilmur"/>


#<a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Criar fluxos de trabalho de codificação avançados estruturador do fluxo de trabalho

##<a name="overview"></a>Descrição geral

O **Estruturador de fluxo de trabalho** é uma ferramenta de ambiente de trabalho do Windows que é utilizada para estruturar e construir fluxos de trabalho personalizados para a codificação com **o fluxo de trabalho do Media Encoder Premium**.
Ao utilizar o power da ferramenta de estruturador do fluxo de trabalho, pode estruturar e criar fluxos de trabalho complexos que serão executado no **Media Encoder Premium**.  

Fluxos de trabalho podem incluir lógica de decisão de cliente e ramificação com base em Propriedades do ficheiro de origem de entrada. Pode criar fluxos de trabalho com as propriedades overridable e valores dinâmicos para facilitar a repetir e personalizar na nuvem do mesmo as codificação tarefas mais complexas.

Fluxos de trabalho de exemplo que pode criar incluem:

- Decisão baseada fluxos de trabalho que inspecionar o conteúdo de origem para a resolução e descodificar apenas as faixas de saída pretendido.  Este é helfpul eliminando às faixas desperdiçadas que iria ser geradas pelo upscaling a haver de conteúdo de origem.
- Vários ficheiros de entrada podem ser utilizados para suportar legendas, sobreposições e lombado conteúdo em conjunto. 

Esta ferramenta também pode ser utilizada para modificar qualquer um dos nossos [publicados fluxos de trabalho](media-services-workflow-designer.md#existing_workflows). 

>[AZURE.NOTE]Para obter a sua cópia da ferramenta do estruturador de fluxo de trabalho, contacte o suportehttp mepd@microsoft.com.


Assim que seja criado um ficheiro de fluxo de trabalho, pode ser carregado como um ativo e, em seguida, utilizada para a codificação de ficheiros de multimédia. Para obter informações sobre como codificar com **o fluxo de trabalho do Media Encoder Premium** utilizando o **.NET**, consulte o artigo [Avançadas codificação com o fluxo de trabalho do Media Encoder Premium](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modificar fluxos de trabalho existentes

A predefinição [publicados fluxos de trabalho](media-services-workflow-designer.md#existing_workflows) podem ser modificadas utilizando a ferramenta de designer. Pode obter a predefinição ficheiros de fluxo de trabalho [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição destes ficheiros.

Os vídeos seguintes demonstram como utilizar o estruturador.

###<a name="day-1--getting-started"></a>Dia 1-Introdução

Vídeo de 1 dia abrange:

- Descrição geral do estruturador
- Fluxos de trabalho básicos – "Olá"
- Criar múltiplas de saída de ficheiros de MP4 para utilização com transmissão dos serviços de multimédia do Azure

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###<a name="day-2"></a>Dia 2

Vídeo 2 de dia abrange:

- Cenários de ficheiro de origem de variável – tratamento de áudio
- Fluxos de trabalho com lógica avançada
- Fases do gráfico

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###<a name="day-3"></a>Dia 3

Vídeo 3 de dia abrange:

- Scripting dentro de fluxos de trabalho/plantas
- Restrições com o codificador atual
- As perguntas e respostas
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]


## <a name="next-step"></a>Passo seguinte

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


Se necessitar de suporte ou tem perguntas sobre a criação de fluxos de trabalho personalizados na ferramenta de estruturador de fluxo de trabalho, envie uma mensagem de correio electrónico mepd@microsoft.com.

##<a name="see-also"></a>Consulte também

[Vídeos de formação estruturador do fluxo de trabalho de codificador do Azure Premium](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)
