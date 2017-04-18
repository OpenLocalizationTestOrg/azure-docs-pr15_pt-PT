<properties 
    pageTitle="Criar filtros com os serviços de multimédia do Microsoft Azure .NET SDK" 
    description="Este tópico descreve como criar filtros para que o cliente pode utilizá-los a secções específicas da cadeia de numa sequência. Dos serviços de multimédia cria manifestos dinâmicos para atingir este transmissão selective." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="07/18/2016"
    ms.author="juliako;cenkdin"/>


#<a name="creating-filters-with-azure-media-services-net-sdk"></a>Criar filtros com os serviços de multimédia do Microsoft Azure .NET SDK

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [RESTO](media-services-rest-dynamic-manifest.md)

Começando lançamento 2.11, dos serviços de multimédia permite-lhe definir filtros para os seus ativos. Filtros destas são as regras do lado do servidor que irão permitir que os seus clientes optar por fazer coisas como: reprodução apenas uma secção de um vídeo (em vez de a reproduzir o vídeo inteiro), ou especificar apenas um subconjunto de áudio e vídeo representações que dispositivo do seu cliente pode processar (em vez de todas as representações que estão associadas o elementos). Esta filtragem dos seus ativos é obtida através de s **Manifesto dinâmicos**que são criados a pedido do seu cliente para transmitir em fluxo um vídeo com base em filtros adicionais especificado.

Para informações mais detalhadas relacionados com a filtros e manifesto dinâmicos, consulte o artigo [Descrição geral de manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

Este tópico mostra como utilizar os serviços de multimédia .NET SDK para criar, atualizar e eliminar filtros. 


Nota: Se atualizar um filtro, pode demorar até 2 minutos para transmissão ponto final para atualizar as regras. Se o conteúdo foi servido utilizando este filtro (e em cache no proxies e CDN caches), atualizar este filtro pode resultar em falhas do leitor. Recomenda-se para limpar a cache após atualizar o filtro. Se esta opção não é possível, considere utilizar um filtro diferente. 

##<a name="types-used-to-create-filters"></a>Tipos de utilizados para criar filtros

Os seguintes tipos de são utilizados quando criar filtros: 

- **IStreamingFilter**.  Este tipo de baseia-se o seguinte REST API [filtro](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- **IStreamingAssetFilter**. Este tipo de baseia-se a REST API seguintes [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- **PresentationTimeRange**. Este tipo de baseia-se a REST API seguintes [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- **FilterTrackSelectStatement** e **IFilterTrackPropertyCondition**. Estes tipos são baseados no seguinte REST APIs [FilterTrackSelect e FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)


##<a name="createupdatereaddelete-global-filters"></a>Criar/actualizar/leitura/eliminar globais filtros

O código seguinte mostra como utilizar o .NET para criar, atualizar, leia e eliminar filtros de elementos.
    
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
                
    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
    
    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);
    
    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
    
    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();
    
    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


##<a name="createupdatereaddelete-asset-filters"></a>Filtros de elementos de criar/actualizar/leitura/eliminar

O código seguinte mostra como utilizar o .NET para criar, atualizar, leia e eliminar filtros de elementos.

    
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
    
        
    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());
    
    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);
    
    filter.Update();
    
    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);
    
    // Delete
    filterUpdated.Delete();
    



##<a name="build-streaming-urls-that-use-filters"></a>Construir a transmissão URLs que utilizam os filtros

Para obter informações sobre como publicar e transmitir a sua elementos, consulte o artigo [Entrega de conteúdos para a descrição geral de clientes](media-services-deliver-content-overview.md).


Os exemplos seguintes mostram como adicionar filtros ao seu URLs transmissão.

**MPEG TRAVESSÃO** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live transmissão (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live transmissão (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Transmissão suaves**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Consulte também 

[Descrição geral de manifestos dinâmicos](media-services-dynamic-manifest-overview.md)
 

