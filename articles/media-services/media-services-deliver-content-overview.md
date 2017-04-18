<properties
    pageTitle="Distribuir conteúdo aos clientes | Microsoft Azure"
    description="Este tópico fornece uma descrição geral do que é, normalmente fornecer o seu conteúdo com os serviços de multimédia do Azure."
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


# <a name="deliver-content-to-customers"></a>Entregar conteúdos a clientes
Quando estiver a realizar o conteúdo de transmissão ou invés aos clientes, o seu objetivo é a vídeo de alta qualidade para vários dispositivos em condições de rede diferente.

Para realizar este objetivo, pode:

- Codificar a sua sequência para uma sequência de vídeo multi velocidade de bits (ajustável velocidade). Isto irá encarregam-se de condições de qualidade e de rede.
- Utilize dos serviços de multimédia do Microsoft Azure [embalagem dinâmica](media-services-dynamic-packaging-overview.md) para dinamicamente compactar voltar a transmissão em sequência para protocolos diferentes. Isto irá encarregam-se da transmissão em dispositivos diferentes. Dos serviços de multimédia suporta a entrega da velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG-TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas).

Este artigo fornece uma descrição geral dos conceitos de entrega de conteúdos importantes.

Para verificar problemas conhecidos, consulte o artigo [problemas conhecidos](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Embalagem dinâmica

Com a embalagem dinâmica que fornece dos serviços de multimédia, pode fornecer conteúdos do seu velocidade ajustável MP4 ou transmissão suaves codificado no transmissão formatos suportados dos serviços de multimédia (MPEG-TRAÇO, HLS, transmissão suaves, HDS) sem ter de voltar Compactar para estes formatos de transmissão. Recomendamos que faz o seu conteúdo com embalagem dinâmico.

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro mezzanine (origem) para um conjunto de ficheiros de MP4 ajustável velocidade ou transmissão suaves ajustável velocidade.
- Obter, pelo menos, uma unidade de transmissão da pedido para o ponto final de transmissão a partir do qual pretende distribuir o conteúdo. Para mais informações, consulte o artigo [como dimensionar a pedido transmissão reservadas unidades](media-services-portal-manage-streaming-endpoints.md).

Com embalagem dinâmica, armazenar e pagar para os ficheiros no formato de armazenamento única. Dos serviços de multimédia irá criar e servir a resposta adequada com base em pedidos de.

Para além de facultar o acesso às capacidades da embalagem dinâmicos, a pedido transmissão unidades reservadas fornece-lhe com capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Por predefinição, a transmissão a pedido está configurado num modelo de instância partilhadas para o qual o servidor recursos (por exemplo, capacidade cluster ou saída) são partilhados com todos os outros utilizadores. Pode melhorar um débito transmissão a pedido por compra de unidades reservadas de transmissão a pedido.

Para mais informações, consulte o artigo [embalagem dinâmica](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos

Pode definir filtros para os seus ativos com os serviços de multimédia. Filtros destas são as regras do lado do servidor que ajudam os seus clientes fazer coisas como reproduzir uma secção específica de um vídeo ou especificar um subconjunto de áudio e vídeo representações que dispositivo do seu cliente pode processar (em vez de todas as representações que estão associadas o elementos). Esta filtragem é obtida através de *manifestos dinâmicos* que são criados ao seu cliente pede para transmitir em fluxo um vídeo com base numa ou mais filtros especificados.

Para mais informações, consulte o artigo [filtros e manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a>Locator

Para fornecer o utilizador com um URL que pode ser utilizado para transmitir em fluxo ou transferir o seu conteúdo, tem primeiro de publicar o seu activo criando um localizador. Um localizador de fornece um ponto de entrada para aceder aos ficheiros contidos de um ativo. Dos serviços de multimédia suporta dois tipos de Locator:

- OnDemandOrigin Locator. Estas são utilizadas para transmitir multimédia em sequência (por exemplo, MPEG-TRAÇO, HLS ou transmissão suave) ou gradualmente transferir ficheiros.
-  Locator de URL do acesso partilhado assinatura (SA). Estas são utilizadas para transferir ficheiros de multimédia para o seu computador local.

Uma *política de acesso* é utilizado para definir as permissões (tal como ler, escrever e lista) e a duração para o qual um cliente tem acesso a um determinado activo. Tenha em atenção que a permissão de lista (AccessPermissions.List) não deve ser utilizada na criação de um locator OrDemandOrigin.

Locator têm datas de expiração. Portal do Azure define uma data de validade 100 anos no futuro para Locator.

>[AZURE.NOTE] Se tiver utilizado o Azure portal para criar Locator antes de Março de 2015, essas Locator foram definida para expirar depois de dois anos.

Para atualizar uma data de validade um locator, utilize [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) APIs. Tenha em atenção que quando atualiza a data de expiração de um localizador de SA, o URL for alterado.

Locator não foram concebido para gerir o controlo de acesso por utilizador. Pode dar direitos de acesso diferentes para os utilizadores individuais ao utilizar as soluções de gestão de direitos digitais (DRM). Para obter mais informações, consulte [Proteger multimédia](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Quando cria um locator, poderão existir um atraso de 30 segundos devido ao armazenamento necessário e processos de propagação no armazenamento do Windows Azure.


## <a name="adaptive-streaming"></a>Adaptive transmissão

Tecnologias de velocidade ajustável permitir que as aplicações de leitor de vídeo determinar as condições da rede e selecione a partir de várias bitrates. Quando diminui a comunicação de rede, o cliente pode selecionar uma velocidade inferior para que reprodução possa continuar com baixa qualidade do vídeo. Tal como a melhorar as condições da rede, o cliente pode mudar para uma velocidade superior com melhor qualidade do vídeo. Azure dos serviços de multimédia suporta as seguintes tecnologias de velocidade ajustável: HTTP Live transmissão (HLS), transmissão suaves, MPEG-TRAÇO e HDS.

Para disponibilizar aos utilizadores transmissão URLs, primeiro tem de criar um locator OnDemandOrigin. Criar o localizador de dá-lhe o caminho base para o elemento que contém os conteúdos que pretende para transmitir em fluxo. No entanto, para poder transmitir em fluxo este conteúdo, é necessário modificar este caminho de ainda mais. Para construir um URL completo para o ficheiro de manifesto transmissão, tem de concatenar valor do caminho do localizador e o manifesto (filename.ism) o nome do ficheiro. Em seguida, **acrescente/manifesto** e um formato adequado (se necessário) para o caminho locator.

>[AZURE.NOTE]Também pode passe os seus conteúdos através de uma ligação SSL. Para fazer isto, certifique-se das que transmissão URLs começam por HTTPS.

Apenas pode passe através de SSL se o ponto final de transmissão a partir do qual distribuir o conteúdo que foi criado após 10 de Setembro de 2014. Se o seu URLs transmissão sejam baseiam os pontos finais da transmissão criados após 10 de Setembro de 2014, o URL contém "streaming.mediaservices.windows.net." Transmissão URLs que contêm "origin.mediaservices.windows.net" (o formato antigo) não suportam SSL. Se o URL está no formato antigo e pretende conseguir transmitir em fluxo através de SSL, crie um novo ponto final transmissão. Utilize baseadas o novo ponto final de transmissão de URLs para transmitir em fluxo conteúdo através de SSL.


## <a name="streaming-url-formats"></a>Transmissão de formatos de URL

### <a name="mpeg-dash-format"></a>Formato de TRAÇO MPEG

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=mpd-Time-CSF)



### <a name="apple-http-live-streaming-hls-v4-format"></a>Formato de V4 Apple HTTP Live transmissão (HLS)

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato de V3 Apple HTTP Live transmissão (HLS)

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato de Apple HTTP Live transmissão (HLS) com o filtro só de áudio

Por predefinição, faixas só de áudio estão incluídas na HLS manifesto. Isto é necessário para certificação Apple Store para redes móveis. Neste caso, se um cliente não tem a largura de banda suficiente ou estiver ligado através de uma ligação de 2G, reprodução muda para só de áudio. Isto ajuda a manter a transmissão conteúdo sem a utilização da memória intermédia, mas não existe nenhuma vídeo. Em alguns cenários, poderá ser preferido player memória intermédia sobre só de áudio. Se pretender remover a faixa só de áudio, adicione **só de áudio = false** para o URL.

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl-v3,Audio-Only=FALSE)

Para mais informações, consulte o artigo [suporte de composição manifesto dinâmicos e HLS funcionalidades adicionais de saída](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).


### <a name="smooth-streaming-format"></a>Formato de transmissão suave

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemplo:

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Suave manifesto transmissão 2.0 (manifest legado)

Por predefinição, o formato de manifesto transmissão suaves contém a etiqueta repetição (r tag). No entanto, alguns leitores não suportam a etiqueta r. Os clientes com estes jogadores podem utilizar um formato que desativa a etiqueta r:

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### <a name="hds-for-adobe-primetimeaccess-licensees-only"></a>HDS (para o licenciamento do Adobe quando for necessária/acesso apenas)

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## <a name="progressive-download"></a>Transferir gradual

Com transferir gradual, pode iniciar a reprodução de multimédia antes de todo o ficheiro foi transferido. Não consegue gradualmente transferir .ism * (ismv isma, ismt ficheiros ou ismc).

Para transferir gradualmente conteúdo, utilize o tipo de OnDemandOrigin de locator. O exemplo seguinte mostra o URL que é baseado no tipo OnDemandOrigin de locator:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Tem de desencriptar quaisquer activos encriptados armazenamento que pretende para transmitir em fluxo do serviço de origem para transferência gradual.

## <a name="download"></a>Transferir

Para transferir o seu conteúdo para um dispositivo de cliente, terá de criar um localizador de SA. O localizador de SA dá-lhe acesso ao contentor de armazenamento do Windows Azure onde se encontra o ficheiro. Para criar o URL de transferência, tem de incorporar o nome do ficheiro entre o anfitrião e assinatura SA.

O exemplo seguinte mostra o URL que é baseado no localizador de SA:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

As seguintes considerações aplicam-se:

- Tem de desencriptar quaisquer activos encriptados armazenamento que pretende para transmitir em fluxo do serviço de origem para transferência gradual.
- Transferência de uma que não concluiu dentro de 12 horas irá falhar.

## <a name="streaming-endpoints"></a>Transmissão pontos finais

Um ponto final de transmissão representa um serviço de transmissão que pode entregar conteúdos diretamente para uma aplicação do leitor de cliente ou a uma rede de entrega de conteúdos (CDN) para distribuição ainda mais. A sequência de saída de um serviço de ponto final transmissão pode ser uma sequência live ou de um ativo invés na sua conta dos serviços de multimédia. Também pode controlar a capacidade do serviço de ponto final transmissão para processar às necessidades de largura de banda crescentes, ajustando transmissão reservadas unidades. Deve alocar, pelo menos, uma unidade reservada para aplicações num ambiente de produção. Para mais informações, consulte o artigo [como dimensionar um serviço de multimédia](media-services-portal-manage-streaming-endpoints.md).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="changes-to-smooth-streaming-manifest-version"></a>Alterações ao Streaming suaves manifesto versão

Antes da disponibilização de serviço de Julho de 2016 – quando activos produzidos pela Media Encoder padrão, o fluxo de trabalho do Media Encoder Premium ou o Azure Media Encoder anteriores foram transmitido em sequência utilizando embalagem dinâmica – transmissão suaves manifesto devolvido teria se ajustarem à versão 2.0. Na versão 2.0, as durações de fragmento não utilize os códigos de repetir chamados ('r'). Por exemplo:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Na versão de serviço de Julho de 2016 o manifesto gerado a transmissão suaves está em conformidade com para a versão 2.2, com durações de fragmento utilizar repetição de etiquetas. Por exemplo:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Alguns dos clientes transmissão suaves legados podem não suportar as repetições etiquetas e irão falhar carregar o manifesto. Para mitigar este problema, pode utilizar o parâmetro de formato de manifesto legado **(formato = fmp4 v20)** ou atualizar o seu cliente para a versão mais recente, que suporta repetição de etiquetas. Para mais informações, consulte o artigo [2.0 transmissão suaves](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados

[Atualizar o Locator dos serviços de multimédia após gradual teclas de armazenamento](media-services-roll-storage-access-keys.md)
