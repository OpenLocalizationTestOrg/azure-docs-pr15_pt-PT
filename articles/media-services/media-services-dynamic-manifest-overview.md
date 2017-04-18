<properties 
    pageTitle="Filtros e manifestos dinâmicos | Microsoft Azure" 
    description="Este tópico descreve como criar filtros para que o cliente pode utilizá-los a secções específicas da cadeia de numa sequência. Dos serviços de multimédia cria manifestos dinâmicos para arquivar este transmissão selective." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="cenkdin;juliako"/>

# <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos

Começando lançamento 2.11, dos serviços de multimédia permite-lhe definir filtros para os seus ativos. Filtros destas são as regras do lado do servidor que irão permitir que os seus clientes optar por fazer coisas como: reprodução apenas uma secção de um vídeo (em vez de a reproduzir o vídeo inteiro), ou especificar apenas um subconjunto de áudio e vídeo representações que dispositivo do seu cliente pode processar (em vez de todas as representações que estão associadas o elementos). Esta filtragem dos seus ativos é arquivado através de s **Manifesto dinâmicos**que são criados a pedido do seu cliente para transmitir em fluxo um vídeo com base em filtros adicionais especificado.

Este tópicos aborda cenários comuns na qual através dos filtros de ser muito vantajoso para os seus clientes e ligações para tópicos que demonstram como criar filtros através de programação (neste momento, pode criar filtros com REST APIs apenas).

##<a name="overview"></a>Descrição geral

Quando fornecer o seu conteúdo a clientes (transmissão eventos ao vivo ou invés) o seu objetivo é a um vídeo de alta qualidade para vários dispositivos em condições de rede diferente. Para obter este objetivo, efetue o seguinte:

- codificar sua sequência a sequência de vídeo multi velocidade de bits ([ajustável velocidade](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (Isto irá encarregam-se de condições de qualidade e de rede) e 
- Utilize dos serviços de multimédia [Embalagem dinâmico](media-services-dynamic-packaging-overview.md) para dinamicamente compactar voltar a transmissão em sequência para diferentes protocolos (Isto irá encarregam-se da transmissão em diferentes dispositivos). Dos serviços de multimédia suporta a entrega da velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas). 

###<a name="manifest-files"></a>Ficheiros de manifesto 

Quando codificar um ativo para transmissão de velocidade ajustável, é criado um ficheiro de **manifesto** (lista pessoal) (o ficheiro é baseado em texto ou baseado em XML). O ficheiro **manifesto** inclui transmissão metadados tais como: controlar o tipo (áudio, vídeo ou texto), a registar o nome, hora de início e de fim, velocidade (qualidade), registar idiomas, a janela de apresentação (janela deslizante de duração fixa), o codec de vídeo (FourCC). Também indica do leitor para obter o fragmento de seguinte, fornecendo informações sobre os seguintes reproduzível vídeos fragmentos disponíveis e a sua localização. Fragmentos (ou segmentos) são os "blocos" reais de conteúdo de vídeo.


Eis um exemplo de um ficheiro de manifesto: 

    
    <?xml version="1.0" encoding="UTF-8"?>  
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">
    
    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />
    
    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    </SmoothStreamingMedia>
    
###<a name="dynamic-manifests"></a>Manifestos dinâmicos

Existem [cenários](media-services-dynamic-manifest-overview.md#scenarios) ao seu cliente precisa de mais flexibilidade do que o que é descrito no ficheiro de manifesto o activo predefinido. Por exemplo:

- Dispositivo específico: entregar apenas a e\ou representações especificado especificado faixas de idioma que são suportadas pelo dispositivo que é utilizado para reproduzir o conteúdo ("representação filtragem"). 
- Reduza o manifesto para mostrar um clip subendereço de um evento direto ("sub-clip filtragem").
- Corte o início de um vídeo ("limitar um vídeo").
- Ajuste a janela de apresentação (DVR) para fornecer um comprimento limitado da janela do DVR no leitor ("Ajustar janela apresentação").
 
Para realizar esta flexibilidade, dos serviços de multimédia oferece **Manifestos dinâmico** com base em [filtros](media-services-dynamic-manifest-overview.md#filters)de predefinidos.  Depois de definir os filtros, os seus clientes podem utilizá-los para transmitir em fluxo uma representação específica ou sub-clips do seu vídeo. Seria especificam filtros adicionais no URL transmissão. Filtros podem ser aplicados ao velocidade ajustável transmissão protocolos suportados pelo [Embalagem dinâmico](media-services-dynamic-packaging-overview.md): HLS, MPEG-TRAÇO, transmissão suaves e HDS. Por exemplo:

MPEG TRAÇO URL com o filtro de

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL de transmissão suaves com filtro

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Para mais informações sobre como distribuir o conteúdo e crie a transmissão URLs, consulte o artigo [Descrição geral do conteúdo entregar](media-services-deliver-content-overview.md).


>[AZURE.NOTE]Note que manifestos dinâmico não alterar o activo e o manifesto predefinido para esse activo. O cliente pode optar por pedir uma sequência com ou sem filtros. 


###<a id="filters"></a>Filtros 

Existem dois tipos de elementos filtros: 

- Filtros globais (podem ser aplicadas a qualquer activo na conta dos serviços de multimédia do Azure, tem uma duração da conta) e 
- Filtros locais (apenas podem ser aplicadas a um ativo com a qual foi associado durante a criação com o filtro, ter um tempo de vida do elemento). 

Tipos de filtro global e locais tem exatamente as mesmas propriedades. A diferença entre os dois principal é para quais os cenários que tipo de um filer é mais adequado. Filtros globais são geralmente adequados para os perfis de dispositivo (representação filtragem) onde locais filtros podem ser utilizados para recortar um activo específico.


##<a id="scenarios"></a>Cenários comuns 

Tal como foi mencionado anteriormente, quando fornecer o seu conteúdo a clientes (transmissão eventos ao vivo ou invés) o seu objetivo é a um vídeo de alta qualidade para vários dispositivos em condições de rede diferente. Além disso, o seu pode ter outros requisitos que envolvam filtrar os seus ativos e utilizar de **Manifesto dinâmico**s. As secções seguintes fornecem uma breve descrição geral dos diferentes cenários de filtragem.

- Especifique apenas um subconjunto de representações de áudio e vídeo que podem processar determinados dispositivos (em vez de todas as representações que estão associadas o elementos). 
- Reproduzir apenas uma secção de um vídeo (em vez de reproduzir o vídeo inteiro).
- Ajuste a janela de apresentação DVR.

##<a name="rendition-filtering"></a>Filtragem de representação de imagens 

Pode optar por codificar seu elementos para vários perfis de codificação (do plano base de h. 264, h. 264 alto, AACL, AACH, Dolby Digital Plus) e várias bitrates de qualidade. No entanto, nem todos os dispositivos de cliente irão suportar perfis e bitrates todos os seus ativos. Por exemplo, o mais antigos dispositivos Android suporta apenas h. 264 do plano base + AACL. Enviar bitrates mais elevados para um dispositivo que não é possível obter as vantagens, resíduos cálculo de largura de banda e do dispositivo. Tal dispositivo tem descodificar todas as informações determinadas, apenas para dimensioná-lo para baixo para visualização.

Com o Manifest dinâmico, pode criar os perfis de dispositivo, como mobile, console, HD/SD, etc. e incluem as pistas e qualidade que pretende fazer uma parte de cada perfil.

 
![Exemplo de filtragem de representação de imagens][renditions2]

No seguinte exemplo, um codificador foi utilizado para codificar um activo mezzanine para sete ISO MP4s vídeos representações (a partir do 180p para 1080p). Elemento codificado pode dinamicamente empacotado em qualquer um dos seguintes protocolos transmissão: HLS, suave, MPEG TRAÇO e HDS.  Na parte superior do diagrama, é apresentado o manifesto HLS para o ativo com sem filtros (contém todas as sete representações).  No canto inferior esquerdo, é apresentado o manifesto HLS ao qual foi aplicado um filtro com o nome "ott". Especifica o filtro de "ott" para remover todos os bitrates abaixo 1Mbps, que resultou nos níveis de qualidade duas inferior retirados na resposta.  Na parte inferior direita, é apresentado o manifesto HLS ao qual foi aplicado um filtro com o nome "móvel". Especifica o filtro "móvel" para remover representações onde a resolução for maior que 720p que resultou nas duas representações 1080p a ser retiradas.

![Filtragem de representação de imagens][renditions1]

##<a name="removing-language-tracks"></a>A remoção de idioma de faixas

Seus ativos podem incluir vários idiomas áudio como inglês, espanhol, francês, etc. Normalmente, os gestores de Player SDK predefinido seleção de pista de áudio e controla o áudio disponível por seleção de utilizador. É um desafio desenvolver essas SDK Player, requer diferentes implementações ao longo específicas do dispositivo player-quadros. Além disso, algumas plataformas, Player APIs estão limitados e não incluir a funcionalidade de selecção de áudio onde os utilizadores não é possível selecionar ou alterar a faixa de áudio predefinido. Com os filtros de elementos, pode controlar o comportamento através da criação de filtros que incluem apenas idiomas áudio pretendidos.

![A filtragem de faixas de idioma][language_filter]


##<a name="trimming-start-of-an-asset"></a>Limitação por motivos de início de um ativo 

Na maioria dos eventos de transmissão direto, operadores executar alguns testes antes do evento real. Por exemplo, estes podem incluir uma folha em branco da seguinte forma antes do início do evento: "Programa começará momentaneamente". Se o programa é arquivar, o teste e os dados de folha em branco também são arquivados e serão incluídos na apresentação. No entanto, esta informação não deve ser apresentada aos clientes. Com Manifest dinâmico, pode criar um filtro de hora de início e remova os dados indesejados de manifesto.

![Limitação por motivos de início][trim_filter]

##<a name="creating-sub-clips-views-from-a-live-archive"></a>Criar subpastas clips (vistas) a partir de um arquivo direto

Eventos ao vivo muitos são longos a executar e arquivo direto pode incluir vários eventos. Após o evento direto extremidades radiodifusores poderão pretender dividir o arquivo direto para o início do programa lógicos e parar sequências. Publica, em seguida, estes programas virtuais separadamente sem mensagem processamento o arquivo direto e não a criar activos separados (que não irão obter o benefício de fragmentos em cache existentes nas CDNs). Exemplos desses programas virtuais (sub-clips) são os trimestres de um futebol ou jogo de basquetebol, innings no basebol ou eventos individuais de um tarde do programa de Jogos Olímpicos.

Com Manifest dinâmico, pode criar filtros utilizando as horas de início/fim e criar vistas virtuais na parte superior do seu arquivo direto. 

![Filtro de subclip][subclip_filter]

Filtrada elementos:

![Esqui][skiing]

##<a name="adjusting-presentation-window-dvr"></a>Ajustar a janela de apresentação (DVR)

Atualmente, dos serviços de multimédia do Azure oferece arquivo circular onde pode ser configurada a duração entre 5 minutos - horas 25. Filtragem manifesto pode ser utilizado para criar uma janela DVR ficha técnica na parte superior do arquivo, sem eliminar multimédia. Existem muitos cenários onde pretende fornecem uma janela DVR limitada que move-se com a margem direto e ao mesmo tempo manter uma janela de arquivamento maior radiodifusores. Recomendamos que utilize os dados que está fora da janela DVR para realçar clips um emissor ou he\she poderá pretender fornecer janelas DVR diferentes para diferentes dispositivos. Por exemplo, a maior parte dos dispositivos móveis não processa janelas DVR grandes (pode ter uma janela DVR 2 minutos para dispositivos móveis e 1 hora para clientes de ambiente de trabalho).

![Janela DVR][dvr_filter]

##<a name="adjusting-livebackoff-live-position"></a>Ajustar LiveBackoff (posição direto)

Filtragem manifesto pode ser utilizado para remover vários segundos a partir da extremidade direto um programa direto. Esta opção permite-radiodifusores ver a apresentação no ponto de publicação de pré-visualizar e crie anúncio pontos de inserção antes dos visualizadores recebem a sequência (normalmente cópias-off 30 segundos). Radiodifusores, em seguida, podem transmitir estes anúncios para os respetivos quadros cliente hora para a sua recebido e a informação antes da oportunidade de publicidade do processo.

Para além do suporte de publicidade, LiveBackoff pode ser utilizado para ajustar a posição de transferência direto do cliente para que quando os clientes vaguear e clicar no limite direto que ainda possam entrar fragmentos a partir do servidor em vez de receber mensagens de erro HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]


##<a name="combining-multiple-rules-in-a-single-filter"></a>Combinar múltiplas regras num único filtro

Pode combinar múltiplas regras filtragem num único filtro. Por exemplo pode definir uma regra de intervalo para remover folha em branco a partir de um arquivo direto e também filtrar bitrates disponíveis. Para múltiplas regras de filtragem o resultado final é a composição (apenas interseção) destas regras.

![múltiplas regras][multiple-rules]

##<a name="create-filters-programmatically"></a>Criar filtros através de programação

O seguinte tópico aborda entidades dos serviços de multimédia que estão relacionados com filtros. O tópico também mostra como criar filtros através de programação.  

[Criar filtros com a opção REST APIs](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Combinar vários filtros (filtro composição)

Também pode combinar múltiplos filtros num único URL. 

Cenário que se segue demonstra por que razão poderá pretender combinar filtros:

1. É necessário filtrar a qualidade do vídeo para dispositivos móveis como Android ou iPAD (para limitar a qualidade do vídeo). Para remover a qualidade indesejada, pretender criar um filtro global que é adequado para os perfis de dispositivo. Tal como mencionado acima, filtros globais podem ser utilizados para todos os seus ativos na mesma conta de serviços de multimédia sem quaisquer outras associação. 
2. Também pretende cortar a hora de início e de fim de um ativo. Para alcançar isto, teria de criar um filtro local e definir a hora de início/fim. 
3. Que pretende combinar ambos estes filtros (sem combinação que precisa para adicionar a filtragem de qualidade para o filtro de limitação por motivos que tornam a utilização de filtro de difícil acesso).

Para combinar filtros, tem de definir os nomes de filtro à lista manifesto/pessoal URL com ponto e vírgula delimitado por. Imaginemos que tem um filtro com o nome *MyMobileDevice* que filtros qualidade e têm outra com nome *MyStartTime* para definir uma hora de início específico. Pode combiná-los da seguinte forma:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Pode combinar até 3 filtros. 

Para obter mais informações consulte o artigo [neste](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogue.


##<a name="know-issues-and-limitations"></a>Saber problemas e limitações

- Manifesto dinâmico funciona em GOP limites (tecla pacotes), por conseguinte, limitar tem precisão GOP. 
- Pode utilizar o mesmo nome de filtro para filtros globais e locais. Tenha em atenção que filtragem local têm precedência e irá substituir filtros globais.
- Se atualizar um filtro, pode demorar até 2 minutos para transmissão ponto final atualizar as regras. Se o conteúdo foi servido através dos filtros de algumas (e em cache no proxies e CDN caches), atualizar estes filtros pode resultar em falhas do leitor. Recomenda-se para limpar a cache após atualizar o filtro. Se esta opção não é possível, considere utilizar um filtro diferente.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Consulte também

[Fornecer o conteúdo a descrição geral de clientes](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
 