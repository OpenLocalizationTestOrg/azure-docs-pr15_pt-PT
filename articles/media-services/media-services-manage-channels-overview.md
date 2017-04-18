<properties 
    pageTitle="Descrição geral do Live vapor utilizando os serviços de multimédia do Azure | Microsoft Azure" 
    description="Este tópico fornece uma descrição geral de vapor live utilizando os serviços de multimédia do Azure." 
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
    ms.date="10/17/2016"
    ms.author="juliako"/>

#<a name="overview-of-live-steaming-using-azure-media-services"></a>Descrição geral do Live vapor utilizando os serviços de multimédia do Azure

##<a name="overview"></a>Descrição geral

Quando a realizar a transmissão eventos ao vivo com os serviços de multimédia do Azure os seguintes componentes frequentemente estão, normalmente:

- Uma câmara que é utilizada para difundir um evento.
- Descodificador vídeo direto que converte sinais da câmara sequências que são enviadas para um serviço de transmissão direto.

    Opcionalmente, hora de direto vários sincronizada codificadores. Crítico para determinados eventos ao vivo que disponibilidade muito alta pedido e qualidade da experiência, recomenda-se para empregam activo activo codificadores redundantes com a sincronização de tempo para alcançar totalmente integrada activação pós-falha sem perda de dados.
- Um serviço transmissão direto que permite-lhe fazer o seguinte:
    
    - ingerir esta última direto conteúdo utilizando vários protocolos de transmissão direto (por exemplo RTMP ou transmissão suave),
    - codificar (opcionalmente) da sua cadeia em sequência de velocidade ajustável
    - Pré-visualizar o fluxo direto,
    - gravar e armazenar o conteúdo aspirado para poder ser transmitido em sequência posterior (invés)
    - entrega o conteúdo através de protocolos comuns de transmissão (por exemplo, MPEG travessão, suave, HLS, HDS) diretamente aos seus clientes ou a rede de entrega um conteúdo (CDN) para distribuição ainda mais.


**Serviços de multimédia do Microsoft Azure** (MGA) fornece a capacidade de ingerir esta última, codificar, pré-visualizar, armazenar e distribuir o conteúdo de transmissão direto.

Quando fornecer o seu conteúdo a clientes o seu objetivo é a um vídeo de alta qualidade para vários dispositivos em condições de rede diferente. Para alcançar isto, utilize codificadores direto codificar a sua sequência para uma sequência de vídeo do multi velocidade (ajustável velocidade).  Para encarregam-se da transmissão em dispositivos diferentes, utilize dos serviços de multimédia [embalagem dinâmica](media-services-dynamic-packaging-overview.md) para dinamicamente compactar novamente a sua sequência para protocolos diferentes. Dos serviços de multimédia suporta a entrega da velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas).

Serviços de multimédia do Azure, **canais**, **programas**e **StreamingEndpoints** processam todas as funcionalidades transmissão direto incluindo ingest, formatação, DVR, segurança, escalabilidade e redundância.

Um **canal** representa uma tubagem para processamento de conteúdo de transmissão direto. Um canal pode receber uma live sequências de entrada das seguintes formas:

- No local ao vivo codificador envia multi velocidade **RTMP** ou **Transmissão suaves** (fragmentados MP4) para o canal que está configurado para entrega de **pass-through** . A entrega de **pass-through** é quando as sequências aspiradas passam **canal**s sem qualquer processamento suplementar. Pode utilizar os seguintes codificadores direto que multi velocidade suaves transmissão de saída: elemento, da Envivio, Cisco.  As seguintes codificadores direto saída RTMP: transcoders Adobe Flash multimédia Live descodificador (FMLE), Telestream Wirecast e Tricaster.  Um codificador direto também pode enviar uma sequência de velocidade único para um canal que não está ativado para codificação direto, mas que não é recomendado. Quando solicitado, dos serviços de multimédia fornece a sequência de aos clientes.

    >[AZURE.NOTE] Utilizar um método pass-through é a forma mais económica de live transmissão quando estão a fazer vários eventos durante um longo período de tempo e já investido codificadores no local. Ver os detalhes do [preços](/pricing/details/media-services/) .
    
    
- Um codificador direto no local envia uma sequência de velocidade único para o canal que está ativado para efetuar a codificação live com os serviços de multimédia de uma das seguintes formatos: RTMP ou suaves transmissão (fragmentados MP4). Também é suportada RTP (MPEG-TS), desde que tenha uma ligação dedicada para o Centro de dados Azure. As seguintes codificadores live com a saída RTMP são conhecidos para trabalhar com canais deste tipo: Telestream Wirecast, FMLE. O canal, em seguida, executa direto codificação da receção sequência de velocidade único para uma sequência de vídeo multi-velocidade de bits (ajustável). Quando solicitado, dos serviços de multimédia fornece a sequência de aos clientes.


Iniciar com a versão 2.10 de serviços de multimédia, ao criar um canal, pode especificar de que forma que pretende para o seu canal receber a sequência de entrada e permissão ou não que pretende para o canal efetuar a codificação direto da sua sequência. Tem duas opções:

- **Nenhum** (pass-through) – especificar este valor, se planeia utilizar um codificador direto no local que irá saída multi velocidade da cadeia (uma sequência pass-through). Neste caso, a sequência de receção passou para a saída sem qualquer codificação. Este é o comportamento de um canal antes de versão 2.10.  

- **Padrão** – escolha este valor, se planeia utilizar dos serviços de multimédia para codificar sua sequência de velocidade única em directo à sequência de velocidade multi. Este método é mais económico para escalar para cima rapidamente para eventos pouco frequentes. Tenha em atenção que existe um impacto faturação para codificação live e deve Lembre-se de que a sair de um canal codificação direto no estado de "Executar" irá implicam taxas de faturaçãohttps.  É recomendado que parar os canais em execução imediatamente após a conclusão para evitar taxas extra hora a hora do seu evento de transmissão direto. 

##<a name="comparison-of-channel-types"></a>Comparação de tipos de canal

Tabela seguinte fornece um guia para comparar os dois tipos de canal suportados nos serviços de multimédia

Funcionalidade|Pass-through canal|Canal padrão
---|---|---
Entrada de velocidade única é codificada para vários bitrates na nuvem|N|Sim
Resolução máxima, número de camadas|1080p, 8 camadas, 60 + fps|720p, 6 camadas, 30 fps
Protocolos de entrada|RTMP, suave transmissão|RTMP, transmissão suaves e RTP
Preço|Ver a [página de preços](/pricing/details/media-services/) e clique no separador "Vídeo Live"|Consulte o artigo [preços de página](/pricing/details/media-services/) 
Máximo tempo de execução|24 x 7|8 horas
Suporte para inserir Lousas|N|Sim
Suporte para sinalização ad|N|Sim
Legendas de CEA 608/708 pass-through|Sim|Sim
Capacidade para recuperar da lugares breves na contribuição feed|Sim|Não (canal começará slating após 6 + segundos sem dados de entrada)
Suporte para que não sejam uniforme GOPs de entrada|Sim|Não – entrada têm de ser corrigida 2sec GOPs
Suporte para introduções de taxa de moldura variável|Sim|Não – entrada deve ser moldura taxa fixa.<br/>Os variações secundárias estão admitidos, por exemplo, durante cenas movimento alta. Mas não é possível largar codificador para 10 molduras/seg.
Automática-desligamento de canais quando entrada feed é perdida|N|Depois de 12 horas, se existir sem um programa a executar 

##<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhar com canais que recebem o fluxo direto multi velocidade de codificadores no local (pass-through)

O diagrama seguinte mostra as partes principais da plataforma AMS que estão, normalmente no fluxo de trabalho **pass-through** .

![Fluxo de trabalho direto](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Para obter mais informações, consulte o artigo [trabalhar com os canais que Multi receção-fluxo direto de velocidade de codificadores no local](media-services-live-streaming-with-onprem-encoders.md).

##<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhar com os canais que estejam ativados para efetuar a codificação live com dos serviços de multimédia do Azure

O diagrama seguinte mostra as partes principais da plataforma AMS que estão, normalmente no fluxo de trabalho Live transmissão onde um canal está ativado para efetuar a codificação live com dos serviços de multimédia.

![Fluxo de trabalho direto](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Para mais informações, consulte o artigo [trabalhar com os canais que estão activados para executar Live codificação com dos serviços de multimédia do Azure](media-services-manage-live-encoder-enabled-channels.md).

##<a name="description-of-a-channel-and-its-related-components"></a>Descrição de um canal e os seus componentes relacionados

###<a name="channel"></a>Canal

Serviços de multimédia, s [canal](https://msdn.microsoft.com/library/azure/dn783458.aspx)responsável pelo processamento de conteúdo de transmissão direto. Um canal fornece um ponto final de entrada (ingerir esta última URL) que forneça, em seguida, para um live transcoder. O canal recebe sequências de teclado direto a partir do transcoder direto e disponibiliza para transmissão através de um ou mais StreamingEndpoints. Canais também fornecem um pré-visualização ponto final de (URL de pré-visualização) que utilizar para a pré-visualizar e validar da sua cadeia antes de processamento suplementar e de entrega.

Pode obter o URL de ingest e o URL de pré-visualização quando criar o canal. Para obter estes URLs, o canal não tem de ser no estado de iniciado. Quando estiver pronto para começar a conduza dados a partir de um transcoder direto para o canal, tem de ser iniciado o canal. Assim que a transcoder direto é iniciado ingesting dados, pode pré-visualizar a sua sequência.

Cada conta dos serviços de multimédia pode conter vários canais, vários programas e StreamingEndpoints múltiplos. Consoante as necessidades de largura de banda e segurança, podem ser dedicados StreamingEndpoint serviços para um ou mais canais. Qualquer StreamingEndpoint pode extrair a partir de qualquer canal.


###<a name="program"></a>Programa 

Um [programa](https://msdn.microsoft.com/library/azure/dn783463.aspx) permite-lhe controlar a publicação e armazenamento dos segmentos numa sequência direto. Canais gerir programas. A relação de canal e o programa é muito semelhante a multimédia tradicional onde um canal tem uma constante sequência de conteúdo e um programa está confinado a algumas evento temporizado desse canal.
Pode especificar o número de horas que pretende manter o conteúdo para o programa gravado definindo a propriedade **ArchiveWindowLength** . Este valor pode ser definido a partir de um mínimo de 5 minutos para um máximo de 25 horas. 

ArchiveWindowLength dita também que a quantidade máxima de clientes de tempo pode atingir trás no tempo da posição atual direto. Programas podem ser executados sobre a quantidade de tempo especificada, mas conteúdo determinado atrás o comprimento de janela continuamente é eliminado. Este valor desta propriedade também determina quanto tempo podem aumentar os manifestos de cliente.

Cada programa está associado um ativo. Para publicar o programa tem de criar um localizador de elemento associado. Está a ter este locator permite-lhe criar um URL de transmissão pode fornecer aos seus clientes.

Um canal suporta até três programas em simultâneo em execução, para que possa criar múltiplas arquivos do mesmo fluxo recebido. Esta opção permite-lhe publicar e arquivar diferentes partes do evento, conforme necessário. Por exemplo, a necessidade de negócio é para arquivar 6 horas de um programa, mas difundir apenas últimos 10 minutos. Para realizar esta tarefa, é necessário criar duas programas em simultâneo em execução. Um programa está definido para arquivar 6 horas do evento, mas o programa não está a ser publicado. O outro programa está definido para arquivar para 10 minutos e este programa é publicado.


##<a name="billing-implications"></a>Implicações de faturação

Um canal começa assim que é transições de estado para "Executar" através da API de faturação.  

A tabela seguinte mostra como os Estados de canal mapeiam para faturaçãohttps Estados-membros no portal do API e Azure. Note que os Estados são um pouco diferentes entre a API e UX Portal. Assim que um canal encontra no estado "Executar" através da API ou no estado "Pronto" ou "Transmissão" no portal do Azure, faturação estará ativa.

Para parar o canal de faturação ainda mais, tem de parar o canal através da API ou no portal do Azure.
É responsável para parar a sua canais quando tiver terminado com o canal. Falha para parar o canal irá resultar em faturação contínua.

>[AZURE.NOTE]Quando trabalha com canais padrão, AMS serão automaticamente desligamento da qualquer canal que ainda se encontra na estado "Executar" 12 horas depois do feed de entrada é perdido e não existem programas em execução. No entanto, ainda pode ser faturada para a hora que o canal estava no estado "Executar".

###<a id="states"></a>Estados Unidos e como são mapeados para o modo de faturação do canal 

O estado atual de um canal. Valores possíveis incluem:

- **Parado**. Este é o estado inicial do canal após a sua criação (a não ser iniciar foi selecionado no portal.) Sem faturação surge neste estado. Neste estado, as propriedades de canal podem ser atualizadas mas transmissão não é permitida.
- **Iniciar**. Está a ser iniciado o canal. Sem faturação surge neste estado. Não existem atualizações ou transmissão é permitido durante este estado. Se ocorre um erro, devolve o canal para o estado de parado.
- **Em execução**. O canal é compatível com a direto fluxos de processamento. Agora é o faturação a utilização. Tem de parar o canal para impedir que ainda mais faturação. 
- **Parar a**. Está a ser parado o canal. Sem faturação ocorre neste estado breves. Não existem atualizações ou transmissão é permitido durante este estado.
- **Eliminar**. Está a ser eliminado o canal. Sem faturação ocorre neste estado breves. Não existem atualizações ou transmissão é permitido durante este estado.

A tabela seguinte mostra como os Estados de canal mapeiam para o modo de faturação. 
 
Estado de canal|Indicadores de IU Portal|É faturação?
---|---|---
Iniciar|Iniciar|Sem (estado breves)
Em execução|Pronto (sem programas em execução)<br/>ou<br/>Transmissão (pelo menos um programa em execução)|SIM
Parar a|Parar a|Sem (estado breves)
Parado|Parado|N


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="related-topics"></a>Tópicos relacionados

[Serviços de multimédia do Microsoft Azure MP4 fragmentados Live ingerir esta última especificação](media-services-fmp4-live-ingest-overview.md)

[Trabalhar com os canais que estão activados para executar Live codificação com dos serviços de multimédia do Azure](media-services-manage-live-encoder-enabled-channels.md)

[Trabalhar com canais que recebem o fluxo direto Multi velocidade de codificadores no local](media-services-live-streaming-with-onprem-encoders.md)

[Quotas e limitações](media-services-quotas-and-limitations.md).  

[Conceitos dos serviços de multimédia](media-services-concepts.md)
