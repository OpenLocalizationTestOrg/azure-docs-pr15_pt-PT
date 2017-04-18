<properties 
    pageTitle="Azure conceitos dos serviços de multimédia | Microsoft Azure" 
    description="Este tópico fornece uma descrição geral dos conceitos de serviços de multimédia do Azure" 
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

#<a name="azure-media-services-concepts"></a>Azure conceitos dos serviços de multimédia 

Este tópico fornece uma descrição geral dos conceitos dos serviços de multimédia mais importantes.

##<a id="assets"></a>Ativos e armazenamento

###<a name="assets"></a>Elementos

Um [recurso](https://msdn.microsoft.com/library/azure/hh974277.aspx) contém ficheiros digitais (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legenda fechada) e os metadados sobre estes ficheiros. Depois dos ficheiros digitais são carregados num ativo, podem ser utilizadas nos serviços de multimédia codificação e transmissão fluxos de trabalho.

Um ativo é mapeado para um contentor blob na conta de armazenamento do Windows Azure e os ficheiros no activo estão armazenados como blobs desse contentor.

Quando a decidir qual o conteúdo multimédia para carregar e armazenar num activo, aplicam-se as seguintes considerações:

- Um ativo deve conter apenas uma única e exclusiva instância do conteúdo de multimédia. Por exemplo, uma edição única de uma episódio TV, filme ou anúncio.
- Um ativo não deve conter várias representações ou edições de um ficheiro audiovisual. Um exemplo de uma utilização incorreta de um ativo seria tentar armazenar mais do que um episódio da TV, anúncio ou vários ângulos de câmara a partir de uma única de produção dentro de um ativo. Armazenar vários representações ou edições de um ficheiro audiovisual num activo pode resultar em dificuldades submeter codificação tarefas, streaming e proteger a entrega de elemento mais tarde no fluxo de trabalho.  

###<a name="asset-file"></a>Ficheiro de elementos 
Um [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) representa um ficheiro de áudio ou vídeo real que está armazenado num contentor de Blobs. Um ficheiro de elementos sempre está associado um ativo e um ativo pode conter um ou vários ficheiros. A tarefa de serviços de Media Encoder falha se um objeto de ficheiro de elementos não está associado um ficheiro digital num contentor de Blobs.

A instância **AssetFile** e no ficheiro de multimédia real estão dois objetos distintos. A instância AssetFile contém metadados sobre o ficheiro de multimédia, enquanto o ficheiro de multimédia contém os conteúdos de multimédia real.

Não deve tentar alterar o conteúdo de contentores blob que foram geradas por dos serviços de multimédia sem utilizar a API do serviço de multimédia.

###<a name="asset-encryption-options"></a>Opções de encriptação de elementos

Dependendo do tipo de conteúdo que pretende carregar, armazenar e entregar, dos serviços de multimédia fornece várias opções de encriptação que pode escolher a partir de.

**Nenhum** Sem encriptação é utilizada. Este é o valor predefinido. Tenha em atenção que ao utilizar esta opção seu conteúdo não está protegido quando estão em trânsito ou no resto no armazenamento.

Se planear entregar uma MP4 utilizando transferência gradual, utilize esta opção para carregar o seu conteúdo.

**StorageEncrypted** – Utilize esta opção para encriptar o seu conteúdo limpar localmente utilizar encriptação AES 256 a bits e, em seguida, carregue-o para o armazenamento do Windows Azure onde foi armazenado encriptados em repouso. Elementos protegidos com encriptação de armazenamento são automaticamente não encriptados colocados num sistema de ficheiros encriptados antes de codificação e, opcionalmente, encriptar novamente antes de carregar novamente como um novo activo de saída. No caso de utilização principal para encriptação de armazenamento é quando que pretende proteger os seus ficheiros de suporte de entrada de alta qualidade com encriptação forte em repouso no disco. 

Para poder entregar um activo encriptada do armazenamento, tem de configurar políticas de entrega de elementos para que dos serviços de multimédia saibam como pretende distribuir o conteúdo. Antes do recurso pode ser transmitido em sequência, o servidor de transmissão remove a encriptação de armazenamento e transmite em fluxo conteúdo utilizando a política de entrega especificada (por exemplo, AES, PlayReady ou sem encriptação). 

**CommonEncryptionProtected** - Utilize esta opção se pretender encriptar (ou carregar já encriptado) conteúdo com encriptação comuns ou DRM PlayReady (por exemplo, suaves transmissão protegido com PlayReady DRM).

**EnvelopeEncryptionProtected** – Utilize esta opção se quiser proteger (ou carregar já protegido) HTTP Live transmissão (HLS) encriptados com encriptação AES Advanced Standard (). Tenha em atenção que se são carregar HLS já encriptados com AES,-deve ter sido encriptada pelo transformar gestor.

###<a name="access-policy"></a>Política de acesso 

Um [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) define as permissões (como ler, escrever e lista) e a duração do access para um ativo. Faria normalmente verificação ser efetuada com um objeto de AccessPolicy para um localizador de seria, em seguida, utilizado para aceder aos ficheiros de constantes de um ativo.


###<a name="blob-container"></a>Contentor de BLOBs

Um contentor blob fornece um agrupamento de um conjunto de blobs. Blob contentores são utilizadas nos serviços de multimédia como ponto de limite para o controlo de acesso e Locator assinatura partilhadas do Access (SA) em elementos. Uma conta de armazenamento do Windows Azure pode conter um número ilimitado de contentores blob. Um contentor pode armazenar um número ilimitado de blobs.

>[AZURE.NOTE]Não deve tentar alterar o conteúdo de contentores blob que foram geradas por dos serviços de multimédia sem utilizar a API do serviço de multimédia.

###<a id="locators"></a>Locator

[Locator](https://msdn.microsoft.com/library/azure/hh974308.aspx)s fornecem um ponto de entrada para aceder aos ficheiros constantes de um ativo. Uma política de acesso é utilizada para definir as permissões e duração que um cliente tem acesso a um determinado activo. Locator pode ter uma relação muitos-para uma com uma política de acesso, tal que Locator diferentes pode fornecer horas de início diferente e tipos de ligação aos clientes diferentes ao utilizar todas as mesmas permissões e definições de duração; No entanto, devido a uma restrição de política de acesso partilhado definida pelo serviços de armazenamento Azure, não pode ter mais de cinco Locator exclusivo associado a um determinado activo ao mesmo tempo. 

Dos serviços de multimédia suporta dois tipos de Locator: Locator OnDemandOrigin, utilizado para transmitir em fluxo multimédia (por exemplo, MPEG travessão, HLS ou transmissão suave) ou transferir gradualmente multimédia e Locator SA URL, utilizado para carregar ou transferir ficheiros de multimédia to\from armazenamento Azure. 

Tenha em atenção que a permissão de lista (AccessPermissions.List) não deve ser utilizada quando criar um locator OrDemandOrigin. 

###<a name="storage-account"></a>Conta de armazenamento

Todo o acesso ao armazenamento do Azure é feito através de uma conta de armazenamento. Pode associar uma conta de serviço de multimédia com um ou mais contas de armazenamento. Uma conta pode conter um número ilimitado de contentores, desde que o tamanho total é em 500TB por conta de armazenamento.  Dos serviços de multimédia fornece ferramentas de nível de SDK para lhe permitir gerir múltiplas contas de armazenamento e carregar o saldo a distribuição dos seus ativos durante a carregar para nestas contas com base em métricas ou a distribuição aleatória. Para mais informações, consulte o artigo trabalhar com o [Armazenamento do Windows Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

##<a name="jobs-and-tasks"></a>Projectos e tarefas

Uma [tarefa](https://msdn.microsoft.com/library/azure/hh974289.aspx) é normalmente utilizado para processo (por exemplo, índice ou codificar) numa apresentação de áudio/vídeo. Se estiver a processar vários vídeos, crie uma tarefa para cada vídeo ser codificado.

Uma tarefa contém metadados sobre o processamento a ser executado. Cada tarefa contém um ou mais s de [tarefa](https://msdn.microsoft.com/library/azure/hh974286.aspx)que especificam uma tarefa de processamento Atómica, seus activos entradas, saída activos, um processador de multimédia e as suas definições associadas. Tarefas dentro de uma tarefa podem ser interligadas alterado, onde os elementos de saída de uma tarefa são fornecido como elemento entrado para a tarefa seguinte. Desta forma uma tarefa pode conter todos o processamento necessário para uma apresentação de multimédia.

##<a id="encoding"></a>Codificação

Azure dos serviços de multimédia fornece várias opções para a codificação de multimédia na nuvem.

Quando começando dos serviços de multimédia, é importante compreender a diferença entre os formatos de ficheiro e de codecs.
Codecs estão o software que implementa dos algoritmos compressão/descompressão Considerando que formatos de ficheiro são contentores que mantenha o vídeo comprimido.

Dos serviços de multimédia fornece embalagem dinâmica que permite-lhe distribuir o conteúdo de velocidade ajustável MP4 ou transmissão suaves codificado no transmissão formatos suportados dos serviços de multimédia (MPEG travessão, HLS, transmissão suaves, HDS) sem ter de voltar Compactar para estes formatos de transmissão.

Para tirar partido da [embalagem dinâmica](media-services-dynamic-packaging-overview.md), terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro mezzanine (origem) para um conjunto de velocidade ajustável MP4 ficheiros ou de velocidade ajustável transmissão suaves (os passos de codificação são demonstrados mais tarde neste tutorial).
- Obter, pelo menos, uma unidade de transmissão da pedido para o ponto final de transmissão a partir do qual planeia entrega o conteúdo. Para mais informações, consulte o artigo [como a pedido transmissão reservadas unidades da escala](media-services-portal-manage-streaming-endpoints.md).

Dos serviços de multimédia suporta o seguinte procedimento no codificadores pedido que são descritos neste artigo:

- [Padrão de descodificador de multimédia](media-services-encode-asset.md#media-encoder-standard)
- [Fluxo de trabalho do Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Para obter informações sobre codificadores suportados, consulte o artigo [codificadores](media-services-encode-asset.md).


##<a name="live-streaming"></a>Live transmissão

Serviços de multimédia do Azure, um canal representa uma tubagem para processamento de conteúdo de transmissão direto. Um canal recebe sequências de teclado direto numa de duas formas:

- Um codificador direto no local envia RTMP velocidade multi ou suaves transmissão (fragmentado MP4) para o canal. Pode utilizar os seguintes codificadores direto que multi velocidade suaves transmissão de saída: elemento, da Envivio, Cisco. As seguintes codificadores direto saída RTMP: transcoders Adobe Flash Live, Telestream Wirecast e Tricaster. As sequências aspiradas passam através de canais sem qualquer processamento suplementar. Quando solicitado, dos serviços de multimédia fornece a sequência de aos clientes.

- Uma sequência de velocidade única (dos seguintes formatos: RTP (MPEG-TS)), RTMP ou suaves transmissão (fragmentado MP4)) é enviada para o canal que está ativado para efetuar a codificação live com dos serviços de multimédia. O canal, em seguida, executa direto codificação da receção sequência de velocidade único para uma sequência de vídeo multi-velocidade de bits (ajustável). Quando solicitado, dos serviços de multimédia fornece a sequência de aos clientes.

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


Para obter mais informações, consulte:

- [Trabalhar com os canais que estão activados para executar Live codificação com dos serviços de multimédia do Azure](media-services-manage-live-encoder-enabled-channels.md)
- [Trabalhar com canais que recebem o fluxo direto Multi velocidade de codificadores no local](media-services-live-streaming-with-onprem-encoders.md)
- [Quotas e limitações](media-services-quotas-and-limitations.md).

##<a name="protecting-content"></a>Proteger conteúdo

###<a name="dynamic-encryption"></a>Encriptação dinâmica

Azure dos serviços de multimédia permite-lhe proteger os ficheiros de multimédia da hora deixa o seu computador através do armazenamento, transformação e entrega de. Dos serviços de multimédia permite-lhe fornecer o seu conteúdo dinamicamente encriptado com encriptação AES (Advanced Standard) (utilizando as chaves de encriptação de 128-bit) e encriptação comuns (CENC) através da PlayReady e/ou Widevine DRM. Dos serviços de multimédia também fornece um serviço para fornecer chaves AES e PlayReady licenças a clientes autorizados.

Atualmente, pode encriptar os seguintes formatos de transmissão: HLS, MPEG TRAÇO e transmissão suaves. Não é possível encriptar HDS transmissão formato ou progressive transferências.

Se pretender para serviços de multimédia encriptar um ativo, tem de associar uma chave de encriptação (CommonEncryption ou EnvelopeEncryption) ao seu ativo e também de configurar políticas de autorização para a chave.

Se pretender transmitir em fluxo um activo encriptada do armazenamento, tem de configurar política de entrega de elementos para poder especificar como pretende entregar a sua ativo.

Quando uma sequência é pedida por um leitor de, dos serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o conteúdo utilizando um envelope (com AES) ou a encriptação comuns (com PlayReady ou Widevine). Para desencriptar a sequência, o leitor irá pedir a chave de serviço de entrega chave. Para decidir se ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou na chave.


###<a name="token-restriction"></a>Restrição token

A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir, token restrição ou restrição de IP. A política de restrita token tem de ser acompanhar por um token emitido por uma seguro Token serviço (STS). Dos serviços de multimédia suporta tokens no formato de Tokens de Web simples (SWT) e o formato de JSON Web Token (JWT). Dos serviços de multimédia não fornece serviços Token seguro. Pode criar um STS personalizado ou tirar partido do Microsoft Azure ACS aos tokens de problema. O STS tem de ser configurado para criar um token sessão iniciado com as especificado chave e problema em afirmações que especificou na configuração do token de restrição. O serviço de entrega chave dos serviços de multimédia irá devolver chave pedida (ou licença) para o cliente se o token é válido e as afirmações na token correspondem àqueles configurado para a chave (ou licença).

Quando configurar o token restringido a política, tem de especificar a chave primária de verificação, emissor e os parâmetros de audiência. A chave primária verificação contém a chave que foi assinado o token com, emissor é o serviço de tokens seguro que o token de problemas. A audiência (por vezes denominada âmbito) descreve à intenção do token ou o recurso autorize o token de acesso. O serviço de entrega chave dos serviços de multimédia valida que estes valores no token de acordo com os valores no modelo.

Para obter mais informações, consulte os artigos seguintes:

[Descrição geral do conteúdo de proteger](media-services-content-protection-overview.md)
[proteger com AES de 128](media-services-protect-with-aes128.md)
[proteger com DRM](media-services-protect-with-drm.md)

##<a name="delivering"></a>Faz

###<a id="dynamic_packaging"></a>Embalagem dinâmica

Quando trabalha com dos serviços de multimédia recomenda-se para codificar ficheiros mezzanine para uma velocidade ajustável MP4 defina e, em seguida, converter o conjunto para o formato pretendido através da [Embalagem dinâmicos](media-services-dynamic-packaging-overview.md).


###<a name="streaming-endpoint"></a>Transmissão de ponto final

Um StreamingEndpoint representa um serviço de transmissão que pode entregar conteúdos diretamente para uma aplicação do leitor de cliente ou a rede de entrega um conteúdo (CDN) para ainda mais distribuição (dos serviços de multimédia do Azure agora proporciona a integração do Azure CDN). A sequência de saída de um serviço de StreamingEndpoint pode ser uma sequência live ou um vídeo a pedido activo na sua conta dos serviços de multimédia. Além disso, pode controlar a capacidade do serviço de StreamingEndpoint para processar às necessidades de largura de banda crescentes ajustando escala unidades (também conhecido como transmissão). Recomenda-se para atribuir uma ou mais unidades de escala de aplicações no ambiente de produção. Unidades de escala fornecem-lhe com capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidades adicionais que inclui atualmente utilização dinâmico embalagem.

Recomenda-se para utilizar a encriptação dinâmicos do e\ou embalagem dinâmicos. Para utilizar estas funcionalidades, tem de ter, pelo menos, uma unidade de transmissão para o ponto final a partir do qual planeia transmitir em fluxo. Para mais informações, consulte o artigo [dimensionamento transmissão unidades](media-services-portal-manage-streaming-endpoints.md).

Por predefinição pode ter até 2 transmissão pontos finais na sua conta dos serviços de multimédia. Para pedir um limite superior, consulte o artigo [Quotas e limitações](media-services-quotas-and-limitations.md).

Apenas são faturada quando o seu StreamingEndpoint está a executar o estado.

###<a name="asset-delivery-policy"></a>Política de entrega de elementos

Um destes passos no fluxo de trabalho de entrega de conteúdos dos serviços de multimédia está a configurar [políticas de entrega para elementos ](https://msdn.microsoft.com/library/azure/dn799055.aspx)que pretende ser transmitido em sequência. A política de entrega de recurso indica dos serviços de multimédia como que pretende para o seu activo a ser entregue: para o qual a transmissão de protocolo devem seu elementos dinamicamente empacotados (por exemplo, MPEG travessão, HLS, transmissão suaves ou todos), se pretende ou não que encriptar dinamicamente seu ativo e como (envelope ou encriptação comuns).

Se tiver um activo encriptada do armazenamento, antes do recurso pode ser transmitido em sequência, o servidor de transmissão remove a encriptação de armazenamento e transmite em fluxo conteúdo utilizando a política de entrega especificado. Por exemplo, para entregar a sua elementos encriptados com chave de encriptação de encriptação AES Advanced Standard (), defina o tipo de política para DynamicEnvelopeEncryption. Para remover a encriptação de armazenamento e transmitir em fluxo elementos em claro, defina o tipo de política para NoDynamicEncryption.

###<a name="progressive-download"></a>Transferir gradual

Transferir gradual permite-lhe iniciar a reprodução de multimédia antes de todo o ficheiro foi transferido. Pode transferir um ficheiro de MP4 apenas gradualmente.

Tenha em atenção que tem de desencriptar activos encriptados, se pretender para que possam estar disponíveis para transferência gradual.

Para disponibilizar aos utilizadores com URLs de transferência gradual, primeiro tem de criar um locator OnDemandOrigin. Criar o localizador de, dá-lhe o caminho base para o elemento. Em seguida, tem de acrescentar o nome do ficheiro MP4. Por exemplo:

http://amstest1.Streaming.mediaservices.Windows.NET/3c5fe676-199c-4620-9B03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###<a name="streaming-urls"></a>URLs transmissão

Transmissão o seu conteúdo para os clientes. Para disponibilizar aos utilizadores transmissão URLs, primeiro tem de criar um locator OnDemandOrigin. Criar o localizador de, dá-lhe o caminho base para o elemento que contém os conteúdos que pretende para transmitir em fluxo. No entanto, para conseguir transmitir em fluxo este conteúdo é necessário modificar este caminho de ainda mais. Para construir um URL completo para o ficheiro de manifesto transmissão, tem de concatenar valor do caminho do localizador e o manifesto (filename.ism) o nome do ficheiro. Acrescente /Manifest e um formato adequado (se necessário), em seguida, para o caminho locator.

Também pode passe os seus conteúdos através de uma ligação SSL. Para fazer isto, certifique-se das que transmissão URLs começam por HTTPS.

Tenha em atenção que é possível apenas transmitir necessidade através de SSL se o ponto final de transmissão a partir do qual distribuir o conteúdo que foi criado após 10 de Setembro de 2014. Se o seu URLs transmissão sejam baseiam os pontos finais da transmissão criados depois de Setembro de 10, o URL contém "streaming.mediaservices.windows.net" (o novo formato). Transmissão URLs que contêm "origin.mediaservices.windows.net" (o formato antigo) não suportam SSL. Se o URL está no formato antigo e pretende conseguir transmitir em fluxo através de SSL, crie um novo ponto final transmissão. Utilize criadas com baseados no novo ponto final de transmissão de URLs para transmitir em fluxo conteúdo através de SSL.

A lista seguinte descreve os diferentes formatos de transmissão e fornece exemplos:

- Transmissão suaves

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest


- MPEG TRAVESSÃO

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=mpd-Time-CSF)



- Apple HTTP Live transmissão (HLS) V4

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl)



- Apple HTTP Live transmissão (HLS) V3

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=m3u8-aapl-v3)

- HDS (para o licenciamento do Adobe quando for necessária/acesso apenas)

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/MANIFEST(Format=f4m-f4f)


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
