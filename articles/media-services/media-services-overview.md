<properties 
    pageTitle="Descrição geral dos serviços de multimédia Azure e cenários comuns | Microsoft Azure" 
    description="Este tópico fornece uma descrição geral dos serviços de multimédia do Azure" 
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
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>

#<a name="azure-media-services-overview-and-common-scenarios"></a>Descrição geral dos serviços de multimédia Azure e cenários comuns

Dos serviços de multimédia do Microsoft Azure é uma plataforma expansível baseado na nuvem que permite que os programadores criem multimédia dimensionáveis gestão e a entrega de aplicações. Dos serviços de multimédia é baseado REST APIs que permitem-lhe segura carregar, armazenar, codificar e compactar conteúdo de áudio ou vídeo para entrega de transmissão a pedido tanto direto para vários clientes (por exemplo, TV, PC e dispositivos móveis).

Pode criar fluxos de trabalho de ponto a ponto totalmente através dos serviços de multimédia. Também pode optar por utilizar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codificar utilizando um codificador de terceiros. Em seguida, carregar, proteger, compactar, entregar através dos serviços de multimédia.

Pode optar por sequência o conteúdo live ou distribuir conteúdo a pedido. Este tópico mostra cenários comuns para fornecer o seu conteúdo [live](media-services-overview.md#live_scenarios) ou [a pedido](media-services-overview.md#vod_scenarios). O tópico também se liga à outros tópicos relevantes.

## <a name="sdks-and-tools"></a>Ferramentas e SDK

Para criar soluções dos serviços de multimédia, pode utilizar:

- [REST API dos serviços de multimédia](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Um cliente disponível SDK do:
- [Serviços de multimédia do Microsoft azure SDK para .NET](https://github.com/Azure/azure-sdk-for-media-services),
- [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java),
- [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
- [Serviços de multimédia do Microsoft Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Isto é uma versão não Microsoft um SDK Node.js. É mantido por uma Comunidade e atualmente não tem uma cobertura de 100% das AMS APIs).
- Ferramentas de existentes:
- [Portal do Azure](https://portal.azure.com/)
- [Azure-multimédia serviços Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Explorer de serviços de multimédia (AMSE) é um Winforms / aplicação c# para Windows)

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

É possível visualizar AMS aqui caminhos de aprendizagem:

- [AMS Live transmissão em fluxo de trabalho](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS a pedido transmissão de fluxo de trabalho](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##<a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar os serviços de multimédia do Azure, deverá ter o seguinte:

3. Uma conta Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com).
2. Uma conta dos serviços de multimédia do Azure. Utilize o Azure portal, .NET ou REST API para criar a conta dos serviços de multimédia do Azure. Para mais informações, consulte o artigo [Criar conta](media-services-portal-create-account.md).
3. (Opcional) Configure o ambiente de desenvolvimento. Selecione .NET ou REST API do seu ambiente de desenvolvimento. Para mais informações, consulte o artigo [configurar o ambiente](media-services-dotnet-how-to-use.md).

Além disso, saiba como ligar através de programação [Ligar](media-services-dotnet-connect-programmatically.md).
4. (Recomendado) Atribui uma ou mais unidades de escala. Recomenda-se para atribuir uma ou mais unidades de escala de aplicações no ambiente de produção.   Para mais informações, consulte o artigo [Gerir transmissão pontos finais](media-services-portal-manage-streaming-endpoints.md).

##<a name="concepts-and-overview"></a>Conceitos e descrição geral

Para conceitos dos serviços de multimédia do Azure, consulte o artigo [conceitos](media-services-concepts.md).

Para uma série de instruções que lhe apresenta todos os componentes principais dos serviços de multimédia do Azure, consulte o artigo [tutoriais passo a passo do Azure multimédia serviços](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Nesta série tem uma descrição geral dos conceitos e utiliza a ferramenta AMSE para demonstrar tarefas AMS. Tenha em atenção que a ferramenta AMSE é uma ferramenta do Windows. Esta ferramenta suporta a maioria das tarefas que é possível alcançar através de programação com [AMS SDK para .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java)ou [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

##<a id="vod_scenarios"></a>Fornecer suporte a pedido com os serviços de multimédia do Azure: cenários e tarefas comuns

Esta secção descreve cenários comuns e fornece ligações para tópicos relevantes. O diagrama seguinte mostra as partes principais da plataforma dos serviços de multimédia que estão envolvidas na fornecer conteúdo a pedido. 

![VoD fluxo de trabalho](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###<a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Proteger conteúdo no armazenamento e transmitir a transmissão de multimédia em claro (não encriptado)

1. Carregar um ficheiro de alta qualidade mezzanine para um ativo.
    
    Recomenda-se para aplicar a opção de encriptação de armazenamento a sua elementos para proteger o seu conteúdo durante o carregamento e enquanto em repouso no armazenamento.
 
1. Codificar a um conjunto de ficheiros de velocidade ajustável MP4. 

    Recomenda-se para aplicar a opção de armazenamento da encriptação ao activo de saída para proteger o seu conteúdo em repouso.
    
1. Configure a política de entrega de elementos (utilizada pela embalagem dinâmica). 
    
    Se a elementos armazenamento encriptado, **tem** de configurar política de entrega de elementos. 

1. Publica o activo criando um locator pedido.

    Certifique-se de ter, pelo menos, uma transmissão unidade reservada no ponto final transmissão a partir do qual pretende em sequência de conteúdo.

1. Sequência de publicado conteúdo.

###<a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteger conteúdo no armazenamento, entregue dinamicamente encriptada transmissão de multimédia  

Para poder utilizar a encriptação dinâmica, terá de obter, pelo menos, uma unidade reservada transmissão no ponto final transmissão a partir do qual pretende transmitir em fluxo conteúdo encriptado.

1. Carregar um ficheiro de alta qualidade mezzanine para um ativo. Aplica opção de encriptação de armazenamento a elemento.
1. Codificar a um conjunto de ficheiros de velocidade ajustável MP4. Aplica opção de armazenamento da encriptação ao activo de saída.
1. Crie a chave de conteúdo de encriptação para elementos que pretende ser dinamicamente encriptados durante a reprodução.
2. Configure a política de autorização da chave conteúdo.
1. Configure a política de entrega de elementos (utilizada pela embalagem dinâmica e encriptação dinâmica).
1. Publica o activo criando um locator pedido.
1. Sequência de publicado conteúdo. 

###<a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Utilizar a análise de multimédia para deriva informações acionáveis os seus vídeos 

Análise de multimédia é uma coleção de componentes de voz e visuais que tornam mais fácil para organizações e empresas para obter informações acionáveis a partir dos seus ficheiros de vídeos. Para mais informações, consulte o artigo [Descrição geral do análise de serviços de multimédia do Azure](media-services-analytics-overview.md).

1. Carregar um ficheiro de alta qualidade mezzanine para um ativo.
2. Utilize um dos seguintes serviços de análise de multimédia para os seus vídeos do processo:
    
    - **Indexador** – [vídeos de processo com 2 de indexador de multimédia do Azure](media-services-process-content-with-indexer2.md)
    - **Hyperlapse** – [ficheiros de multimédia de Hyperlapse com Hyperlapse de multimédia do Microsoft Azure](media-services-hyperlapse-content.md)
    - **Deteção de movimento** – [Deteção de movimento para análise de multimédia do Azure](media-services-motion-detection.md).
    - **Deteção de nominal e emoções nominal** – [nominal e deteção de emoções para análise de multimédia do Azure](media-services-face-and-emotion-detection.md).
    - **Resumo do vídeo** – [Utilizar Azure multimédia vídeo miniaturas para criar um resumo de vídeo](media-services-video-summarization.md)
3. Processadores de multimédia de análise de multimédia produzem MP4 ficheiros ou ficheiros JSON. Se um processador de multimédia produzidos um ficheiro de MP4, gradualmente pode transferir o ficheiro. Se um processador de multimédia produzidos um ficheiro JSON, pode transferir o ficheiro a partir do armazenamento de Blobs do Azure. 


###<a name="deliver-progressive-download"></a>Entregar transferência gradual 

1. Carregar um ficheiro de alta qualidade mezzanine para um ativo.
1. Codificar num único ficheiro MP4.
1. Publica o activo através da criação de um pedido ou SA locator.

    Se utilizar o localizador de pedido, certifique-se de ter, pelo menos, uma transmissão unidade reservada no ponto final transmissão a partir do qual planeia gradualmente transferir conteúdo.

    Se utilizar o localizador de SA, o conteúdo é transferido a partir do armazenamento de Blobs do Azure. Neste caso, não tem de ter a transmissão reservadas unidades.
  
1. Transferir gradualmente conteúdo.

##<a id="live_scenarios"></a>Entregar eventos transmissão ao vivo com os serviços de multimédia do Microsoft Azure

Quando trabalha com Live transmissão os seguintes componentes frequentemente estão, normalmente:

- Uma câmara que é utilizada para difundir um evento.
- Descodificador vídeo direto que converte sinais da câmara sequências que são enviadas para um serviço de transmissão direto.

Opcionalmente, hora de direto vários sincronizada codificadores. Crítico para determinados eventos ao vivo que disponibilidade muito alta pedido e qualidade da experiência, recomenda-se para empregam activo activo codificadores redundantes com hora synchronizationto alcançar totalmente integrada activação pós-falha sem perda de dados.
- Um serviço transmissão direto que permite-lhe fazer o seguinte:

- ingerir esta última direto conteúdo utilizando vários protocolos de transmissão direto (por exemplo RTMP ou transmissão suave),
- codificar (opcionalmente) da sua cadeia em sequência de velocidade ajustável
- Pré-visualizar o fluxo direto,
- gravar e armazenar o conteúdo aspirado para poder ser transmitido em sequência posterior (invés)
- entrega o conteúdo através de protocolos comuns de transmissão (por exemplo, MPEG travessão, suave, HLS, HDS) diretamente aos seus clientes ou a rede de entrega um conteúdo (CDN) para distribuição ainda mais.


**Serviços de multimédia do Microsoft Azure** (MGA) fornece a capacidade de ingerir esta última, codificar, pré-visualizar, armazenar e distribuir o conteúdo de transmissão direto.

Quando fornecer o seu conteúdo a clientes o seu objetivo é a um vídeo de alta qualidade para vários dispositivos em condições de rede diferente. Para encarregam-se de condições de qualidade e de rede, utilize codificadores direto codificar a sua sequência a sequência de vídeo multi velocidade (ajustável velocidade).  Para encarregam-se da transmissão em dispositivos diferentes, utilize dos serviços de multimédia [embalagem dinâmica](media-services-dynamic-packaging-overview.md) para dinamicamente compactar novamente a sua sequência para protocolos diferentes. Dos serviços de multimédia suporta a entrega da velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas).

Serviços de multimédia do Azure, **canais**, **programas**e **StreamingEndpoints** processam todas as funcionalidades transmissão direto incluindo ingest, formatação, DVR, segurança, escalabilidade e redundância.

Um **canal** representa uma tubagem para processamento de conteúdo de transmissão direto. Um canal pode receber uma live sequências de entrada das seguintes formas:

- No local ao vivo codificador envia multi velocidade **RTMP** ou **Transmissão suaves** (fragmentados MP4) para o canal que está configurado para entrega de **pass-through** . A entrega de **pass-through** é quando as sequências aspiradas passam **canal**s sem qualquer processamento suplementar. Pode utilizar os seguintes codificadores direto que multi velocidade suaves transmissão de saída: elemento, da Envivio, Cisco.  As seguintes codificadores direto saída RTMP: transcoders Adobe Flash Live, Telestream Wirecast e Tricaster.  Um codificador direto também pode enviar uma sequência de velocidade único para um canal que não está ativado para codificação direto, mas que não é recomendado. Quando solicitado, dos serviços de multimédia fornece a sequência de aos clientes.

>[AZURE.NOTE] Utilizar um método pass-through é a forma mais económica de live transmissão quando estão a fazer vários eventos durante um longo período de tempo e já investido codificadores no local. Ver os detalhes do [preços](/pricing/details/media-services/) .

- Um codificador direto no local envia uma sequência de velocidade único para o canal que está ativado para efetuar a codificação live com os serviços de multimédia de uma das seguintes formatos: RTP (MPEG-TS), RTMP ou suaves transmissão (fragmentado MP4). O canal, em seguida, executa direto codificação da receção sequência de velocidade único para uma sequência de vídeo multi-velocidade de bits (ajustável). Quando solicitado, dos serviços de multimédia fornece a sequência de aos clientes.


###<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhar com canais que recebem o fluxo direto multi velocidade de codificadores no local (pass-through)

O diagrama seguinte mostra as partes principais da plataforma AMS que estão, normalmente no fluxo de trabalho **pass-through** .

![Fluxo de trabalho direto][live-overview2]

Para obter mais informações, consulte o artigo [trabalhar com os canais que Multi receção-fluxo direto de velocidade de codificadores no local](media-services-live-streaming-with-onprem-encoders.md).

###<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhar com os canais que estejam ativados para efetuar a codificação live com dos serviços de multimédia do Azure

O diagrama seguinte mostra as partes principais da plataforma AMS que estão, normalmente no fluxo de trabalho Live transmissão onde um canal está ativado para efetuar a codificação live com dos serviços de multimédia.

![Fluxo de trabalho direto][live-overview1]

Para mais informações, consulte o artigo [trabalhar com os canais que estão activados para executar Live codificação com dos serviços de multimédia do Azure](media-services-manage-live-encoder-enabled-channels.md).


##<a name="consuming-content"></a>Consumir conteúdo

Azure dos serviços de multimédia fornece as ferramentas necessárias para criar aplicações de leitor de cliente dinâmicos, complexos para a maioria dos plataformas, incluindo: iOS, dispositivos Android, Windows, Windows Phone, Xbox, dispositivos e Set-top caixas. O tópico seguinte fornece ligações para SDK e estruturas de leitor de trabalho que pode utilizar para desenvolver o seus próprio aplicações de cliente podem consumir transmissão de multimédia de serviços de multimédia.

[Desenvolver aplicações de leitor de vídeo](media-services-develop-video-players.md)

##<a name="enabling-azure-cdn"></a>Ativar o Azure CDN

Dos serviços de multimédia suporta a integração com o Azure CDN. Para obter informações sobre como ativar o Azure CDN, consulte [como gerir transmissão pontos finais numa conta de serviços de multimédia](media-services-portal-manage-streaming-endpoints.md).

##<a name="scaling-a-media-services-account"></a>Dimensionamento uma conta dos serviços de multimédia

É possível dimensionar **Dos serviços de multimédia** , especificando o número de **Unidades reservadas transmissão** e **Codificação reservadas unidades** que pretende utilizar a sua conta para aprovisionados com.

Também pode dimensionar sua conta dos serviços de multimédia ao adicionar contas de armazenamento à mesma. Cada conta de armazenamento está limitada a 500 TB. Para expandir o seu armazenamento fora das limitações predefinido, pode optar por anexar várias contas de armazenamento para uma única conta dos serviços de multimédia.

[Este](media-services-portal-scale-streaming-endpoints.md) tópico fornece hiperligações para tópicos relevantes.

##<a name="support"></a>Suporte

[Suporte do Azure](https://azure.microsoft.com/support/options/) fornece opções de suporte para Azure, incluindo dos serviços de multimédia.

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)

- Para codificação de serviços de multimédia, podemos garantir 99,9% de disponibilidade das transações REST API.
- Para transmissão, podemos irá com êxito pedidos de serviço com uma garantia de disponibilidade 99,9% de conteúdo multimédia existente quando pelo menos uma unidade de transmissão é adquirida.
- Para Live canais, podemos garantir que a executar o canais terão conectividade externa, pelo menos, 99,9% das vezes.
- Para protecção de conteúdos, podemos garantir que recomendamos irá com êxito satisfazer os principais pedidos de pelo menos 99,9% das vezes.
- Para indexador, podemos irá com êxito pedidos de serviço indexador tarefa processados com um reservado codificação unidade 99,9% das vezes.

Para mais informações, consulte o artigo [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 
