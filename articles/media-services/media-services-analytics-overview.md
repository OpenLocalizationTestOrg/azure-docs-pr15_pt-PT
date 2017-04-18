<properties
    pageTitle="Descrição geral de análise de serviços de multimédia do Azure | Microsoft Azure"
    description="Azure dos serviços de multimédia oferece a pré-visualização pública de análise de multimédia do Azure, uma coleção de serviços de visão de voz e de computador na escala de empresa, segurança e conformidade alcance global. Serviços de multimédia Analytics Azure foram criados utilizando os componentes principais de plataforma dos serviços de multimédia do Azure e, por conseguinte, estão prontos para lidar com um media processamento de escala de um dia. "
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/24/2016"   
    ms.author="milanga;juliako;johndeu"/>

# <a name="azure-media-services-analytics-overview"></a>Descrição geral de análise de serviços de multimédia do Azure

##<a name="overview"></a>Descrição geral

Mais organizações e empresas são cubra vídeo como meios preferido para formar dos seus colaboradores, contrate os seus clientes e funções de negócio do documento. Nuvem computação torna eficaz para armazenar, transmitir em fluxo e aceder a estes ficheiros de multimédia grandes, mas como empresas aumentar a sua biblioteca de conteúdos vídeo, têm de ter um meio uniforme eficaz para extracção novas informações do vídeo para criar mais importantes personalizada interações com os respetivos audiências e tirar o seu negócio para o próximo nível.

Para resolver este necessidade crescente no mercado, dos serviços de multimédia do Azure oferece Analytics de multimédia, um conjunto de voz e de visão componentes (na escala de empresa, conformidade, segurança e alcance global) que tornam mais fácil para organizações e empresas para obter informações acionáveis a partir dos seus ficheiros de vídeos. Serviços de multimédia Analytics Azure foram criados utilizando os componentes principais de plataforma dos serviços de multimédia do Azure e, por conseguinte, estão prontos para lidar com um media processamento de escala de um dia.

Análise de multimédia Azure permitir que os programadores começar a trabalhar com as capacidades de visão para vídeo na escala limitada e trazer isto funcionalidades avançadas para aplicações rapidamente. Análise de multimédia Azure baseia-se para ser utilizado por ambientes empresariais com escala completa, conformidade, segurança e alcance global uma ferramenta necessária ao grandes organizações.

O diagrama seguinte mostra **A análise de multimédia** e outras partes principais da plataforma dos serviços de multimédia. 

![VoD fluxo de trabalho](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

Processadores de multimédia de análise de multimédia produzem MP4 ficheiros ou ficheiros JSON. Se um processador de multimédia produzidos um ficheiro de MP4, gradualmente pode transferir o ficheiro. Se um processador de multimédia produzidos um ficheiro JSON, pode transferir o ficheiro a partir do armazenamento de Blobs do Azure. 

## <a name="azure-media-analytics-services"></a>Serviços de multimédia Analytics Azure

- **Indexador** – indexador de multimédia do Azure permite-lhe tornar o conteúdo pesquisável, bem como gerar fechadas faixas legendas. Azure dos serviços de multimédia lançada **Azure multimédia indexador 2 Preview** com o mais depressa indexação e mais amplo suporte de idiomas. Idiomas suportados incluem o inglês, espanhol, francês, alemão, italiano, chinês, português e árabe. Para informações e exemplos detalhados, consulte o artigo [vídeos de processo com 2 de indexador de multimédia do Azure](media-services-process-content-with-indexer2.md)
 
- **Hyperlapse** – Microsoft Hyperlapse é um resultado de mais de vinte anos de pesquisa de visão do computador no Microsoft Research (MSR), combinando estabilização vídeo e tempo caducidade para criar conteúdo rápidos, consumíveis, maravilhosas vídeos a partir do seu formulário longo. Para além de criar cessação de tempo, também pode utilizar Hyperlapse para criar estáveis vídeos a partir de vídeos shaky capturados através de telemóveis e opções. Para informações e exemplos detalhados, consulte o artigo [Ficheiros de multimédia Hyperlapse com Hyperlapse de multimédia do Azure](media-services-hyperlapse-content.md)
 
- **Deteção de movimento** – pode utilizar este serviço para detetar movimento num vídeo com fundos de papel de carta. Este é ideal para clientes que pretende verificar falsos eventos de movimento detetado por câmaras de controlo nos feeds de vídeo do controlo. Para informações e exemplos detalhados, consulte o artigo [Deteção de movimento para análise de multimédia do Azure](media-services-motion-detection.md).
 
- **Deteção de nominal e emoções nominal** – com este serviço, pode detetar faces de pessoas e os respetivos emoções, incluindo felicidade, sadness, inesperada, anger, contempt, receio, repugnantes e indifference/neutro. Isto tem várias indústria útil aplicações, descritas abaixo, incluindo agregar e analisar reacções das pessoas participar um evento. Para informações e exemplos detalhados, consulte o artigo [nominal e deteção de emoções para análise de multimédia do Azure](media-services-face-and-emotion-detection.md).
 
- **Resumo do vídeo** – resumo de vídeo pode ajudar a criar resumos dos vídeos mais longos selecionando automaticamente fragmentos interessantes o vídeo de origem. Isto é útil quando quiser fornecer uma descrição geral do que esperar num vídeo por extenso. Para informações e exemplos detalhados, consulte o artigo [Utilizar Azure multimédia vídeo miniaturas para criar um resumo de vídeo](media-services-video-summarization.md)

- **Ótico de carateres** - OCR de análise de multimédia do Azure (ótico de carateres) permite-lhe converter texto digital editável e pesquisável conteúdo de texto em ficheiros de vídeo. Esta opção permite-lhe automatizar a extração de metadados significativo a partir do sinal de vídeo dos seus ficheiros de multimédia.
 
- **Redaction nominal dimensionáveis** - **Redactor de multimédia do Azure** é uma MP de análise de multimédia do Azure oferece redaction nominal dimensionáveis na nuvem. Nominal redaction permite-lhe modificar o seu vídeo para poder esbater faces dos indivíduos selecionados. Pretende utilizar o serviço de redaction nominal de cenários de segurança e de multimédia de notícias públicos. Alguns minutos de imagens que contenha várias faces podem demorar horas a reter manualmente, mas com este serviço o processo de redaction nominal irão necessitar apenas alguns passos simples. Para mais informações, consulte [Este](media-services-face-redaction.md) artigo.

 
## <a name="common-scenarios"></a>Cenários comuns

Abaixo estão alguns cenários onde a análise de multimédia do Azure pode ajudar as organizações e empresas entre sectores glean novas informações do vídeo para criar mais audiência personalizada e as atribuições de empregado, bem como gerir a forma mais eficaz grande volume de conteúdo de vídeo:

- **Centros de chamada** – par com advento de redes sociais, chamada de cliente centros de ainda facilitar uma grande percentagem de transações do serviço de cliente. Codificado nestes dados áudio é vastos de informações acerca de clientes que podem ser analisadas para melhorar a guias de produto e também dê formação de empregados do Centro de chamada para alcançar mais elevada satisfação do cliente. Ao utilizar indexador de multimédia do Azure, clientes que conseguem extrair texto e criar um índice de pesquisa e dashboards para extrair informações da empresa à volta mais comum queixar, origem dos queixar e outros esses dados relevantes.

- **Moderação de conteúdo gerado pelo utilizador** – a partir de multimédia de notícias tomadas para departamentos policiais, muitas organizações portais opostas públicos onde aceitam multimédia UGC, tais como vídeos e imagens tenham. O volume de conteúdo pode picos devido a eventos inesperados. Nestes cenários, é perto impossibilita realizar uma revisão eficaz manual de conteúdo para adequação. Os clientes podem depender o serviço de moderação de conteúdo para focar o conteúdo adequado.

- **Controlo** - com o crescimento de câmaras IP, existe uma explosão de vídeos de controlo. Rever manualmente vídeo controlo está na altura intensa e sujeito a humano erro. Análise de multimédia Azure fornece vários componentes como deteção de movimento, deteção nominal e Hyperlapse para tornar o processo de revisão, gerir e criação de derivados.

## <a name="media-services-analytics-media-processors"></a>Processadores de multimédia de análise de serviços de multimédia 

Esta secção apresenta todos os a multimédia serviços a análise de multimédia processadores (programa mínimo) e mostra como utiliza o .NET ou resto para obter um objecto MP.

### <a name="mp-names"></a>Nomes de MP


- Pré-visualização do indexador 2 de multimédia do Microsoft Azure
- Indexador de multimédia do Microsoft Azure
- Hyperlapse de multimédia do Microsoft Azure
- Multimédia do Microsoft Azure nominal Detector
- Multimédia do Microsoft Azure Detector de movimento
- Miniaturas de vídeo de multimédia do Microsoft Azure
- Multimédia do Microsoft Azure OCR

### <a name="net"></a>.NET

A função seguinte leva-o até um dos nomes de MP especificados e regressar um objeto de MP.

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


## <a name="rest"></a>RESTO

Pedido:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net
    
Resposta:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

##<a name="demos"></a>Demos

[Azure demos de análise de multimédia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

##<a name="next-steps"></a>Próximos passos

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-articles"></a>Artigos relacionados

[Anúncio de nascimento de análise de serviços de multimédia](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
