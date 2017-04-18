<properties 
    pageTitle="Como criar um processador de multimédia | Microsoft Azure" 
    description="Saiba como criar um componente de processador de multimédia para codificar, converter o formato, encriptar ou desencriptar conteúdo multimédia para serviços de multimédia do Azure. Exemplos de código são gravados no c# e utilizam o SDK de serviços de multimédia para .NET." 
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
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="how-to-get-a-media-processor-instance"></a>Como: obter uma instância de processador de multimédia

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [RESTO](media-services-rest-get-media-processor.md)


##<a name="overview"></a>Descrição geral

Formate dos serviços de multimédia que um processador de multimédia é um componente que se trata de uma tarefa de processamento específico, tal como codificação, conversão, encriptar ou desencriptar conteúdo multimédia. Normalmente, criar um processador de multimédia quando estiver a criar uma tarefa para codificar, encriptar ou converter o formato de conteúdo de multimédia.

A tabela seguinte fornece o nome e descrição de cada processador de multimédia disponíveis.

Nome de processador de multimédia|Descrição|Obter mais informações
---|---|---
Padrão de descodificador de multimédia|Fornece capacidades de padrão para codificação a pedido. |[Descrição geral e comparação do Azure no pedido codificadores de multimédia](media-services-encode-asset.md)
Fluxo de trabalho do Media Encoder Premium|Permite-lhe executar as tarefas codificação utilizando o fluxo de trabalho do Media Encoder Premium.|[Descrição geral e comparação do Azure no pedido codificadores de multimédia](media-services-encode-asset.md)
Indexador de multimédia do Microsoft Azure| Permite-lhe tornar os ficheiros de multimédia e conteúdo pesquisável, bem como gerar faixas legendas fechadas e palavras-chave.|[Indexador de multimédia do Microsoft Azure](media-services-index-content.md)
Azure multimédia Hyperlapse (pré-visualização)|Permite-lhe lisa saída "isentos" no seu vídeo com estabilização vídeo. Também lhe permite acelerar o seu conteúdo para um clip consumíveis.|[Hyperlapse de multimédia do Microsoft Azure](media-services-hyperlapse-content.md)
Descodificador de multimédia do Microsoft Azure|Amortizado
Armazenamento desencriptação| Amortizado|
Gestor de multimédia do Microsoft Azure|Amortizado|
EncDec de multimédia do Microsoft Azure|Amortizado|

##<a name="get-media-processor"></a>Obter processador de multimédia

O método que se segue mostra como obter uma instância de processador de multimédia. O exemplo de código assume a utilização de uma variável de nível do módulo denominada **_context** a referenciar o contexto de servidor, tal como descrito na secção [como: ligar a multimédia serviços através de programação](media-services-dotnet-connect-programmatically.md).

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

##<a name="next-steps"></a>Próximos passos

Agora que sabe como obter uma instância de processador de multimédia, ir para o tópico de [como codificar um ativo](media-services-dotnet-encode-with-media-encoder-standard.md) que lhe mostrará como utilizar o padrão de descodificador de multimédia para descodificar um ativo.


