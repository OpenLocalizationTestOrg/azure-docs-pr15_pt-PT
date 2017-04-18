<properties 
    pageTitle="Notas de lançamento dos serviços de multimédia | Microsoft Azure" 
    description="Notas de lançamento de serviços de multimédia" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="media" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>

# <a name="azure-media-services-release-notes"></a>Notas de lançamento do Azure dos serviços de multimédia

Estas notas de lançamento resumem alterações problemas conhecidos e versões anteriores do.

>[AZURE.NOTE] Pretendemos ouvir a partir dos nossos clientes e concentrar-nos corrigir problemas afectam. Para um problema de um relatório ou fazer perguntas, publique no [Fórum MSDN do Azure multimédia serviços].


##<a id="issues"></a>Problemas conhecidos atualmente

### <a id="general_issues"></a>Serviços de multimédia problemas gerais

Problema|Descrição
---|---
Várias cabeçalhos de HTTP comuns não são fornecidos no REST API.|Se desenvolver aplicações dos serviços de multimédia utilizar a API REST, encontrar que alguns campos comuns em cabeçalhos HTTP (incluindo pedido-ID de cliente, ID de pedido e devolvido-cliente-pedido-ID) não são suportadas. Os cabeçalhos serão adicionados uma atualização futura.
Codificação percentual não é permitida.|Dos serviços de multimédia utiliza o valor da propriedade IAssetFile.Name durante a criação de URLs para o conteúdo da transmissão (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, codificação percentual não é permitida. O valor da propriedade **nome** não pode ter qualquer um dos seguintes [carateres percentagem codificação-reservado](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, só pode haver um '.' para a extensão de nome de ficheiro.
O método de ListBlobs que faz parte do falha do Azure armazenamento SDK versão 3. x.|Dos serviços de multimédia gera URLs SA com base na versão de [2012-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx) . Se pretender utilizar o Azure armazenamento SDK para blobs de lista num contentor de BLOBs, utilize o método de [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) que faz parte da versão do Azure armazenamento SDK 2. x. O método de ListBlobs que faz parte da versão Azure armazenamento SDK 3. x irá falhar.
Serviços de multimédia limitação mecanismo restringe a utilização de recursos para as aplicações que efectuar pedido excessivo ao serviço. O serviço poderá devolver o código de estado do serviço indisponível (503) HTTP.|Para obter mais informações, consulte a descrição do código de estado HTTP 503 no tópico de [Códigos de erro de serviços de multimédia do Azure](http://msdn.microsoft.com/library/azure/dn168949.aspx) .
Quando consultar entidades, existe um limite de 1000 entidades devolvida uma só vez porque público resto v2 limita os resultados da consulta para os resultados de 1000. | Tem de utilizar **Ignorar** e **tomar** (.NET) / **início** (REST) como descrito neste [exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [Este exemplo REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
Alguns clientes podem deparar-se um problema de repetição etiqueta no manifesto transmissão suaves.|Para mais informações, consulte o artigo [nesta](media-services-deliver-content-overview.md#known-issues) secção.
Objetos de multimédia serviços .NET SDK Azure não podem ser feitos em série e como resultado não funcionam com o Azure colocação em cache.|Se tentar serializar o objecto SDK AssetCollection adicioná-lo ao Azure colocação em cache, é devolvida uma exceção.
Tarefas de codificação falharem com uma cadeia de mensagem "fase: DownloadFile. Código: System. NullReferenceException ".|O fluxo de trabalho de codificação típico é carregar ficheiros de vídeos de introdução para um teclado ativo e submeter um ou mais tarefas codificação para esse entrada activo, sem ainda mais modificar que a entrada ativo. No entanto, se modificar o elemento entrado (por exemplo por ficheiros adicionar/eliminar/mudar o nome do elemento), em seguida, as tarefas subsequentes podem falhar com um erro de DownloadFile. A solução é eliminar o elemento entrado e voltar a carregar ficheiros específicos de entrada para um novo imobilizado. 

##<a id="rest_version_history"></a>REST API histórico de versões

Para obter informações sobre o histórico de versões dos serviços de multimédia REST API, consulte o artigo [Referência da API do resto do Azure multimédia serviços].

##<a id="july_changes16"></a>Lançamento de Julho de 2016

###<a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Atualizações do ficheiro de manifesto (*. ISM) gerado pelo codificação de tarefas

Quando uma tarefa codificação é submetida para Media Encoder padrão ou descodificador de multimédia do Azure, a tarefa de codificação gera um [ficheiro de manifesto transmissão](media-services-deliver-content-overview.md) (* .ism) ficheiro na saída ativo. Com a versão mais recente do serviço, a sintaxe deste ficheiro manifesto transmissão foi atualizado.

>[AZURE.NOTE]A sintaxe de ficheiro de sequência manifesto (.ism) é reservada para utilização interna e está sujeito a alterações em versões futuras. Não modificar ou manipular o conteúdo deste ficheiro.

###<a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Um novo manifesto de cliente (*. Ficheiro ISMC) é gerado na saída elementos quando uma tarefa codificação exporta um ou mais ficheiros MP4

Começando com a versão mais recente do serviço, após a conclusão de uma tarefa de codificação que gera um mais MP4 ficheiros, o resultado elementos também irão conter um ficheiro de manifesto (*.ismc) transmissão do cliente. O ficheiro .ismc ajuda a melhorar o desempenho da transmissão dinâmicos. 

>[AZURE.NOTE]A sintaxe do ficheiro de manifesto (.ismc) de cliente é reservada para utilização interna e está sujeito a alterações em versões futuras. Não modificar ou manipular o conteúdo deste ficheiro.

Para mais informações, consulte o artigo [neste](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blogue.

### <a name="known-issues"></a>Problemas conhecidos

Alguns clientes podem ter através de um problema de repetição etiqueta no manifesto transmissão suaves. Para mais informações, consulte o artigo [nesta](media-services-deliver-content-overview.md#known-issues) secção.

##<a id="apr_changes16"></a>Lançamento de Abril de 2016

### <a name="azure-media-analytics"></a>Análise de multimédia do Microsoft Azure

Azure multimédia Servces introduzida análise de multimédia do Azure para análise poderosa de vídeo. Para obter informações detalhadas, consulte o artigo [Descrição geral do análise de serviços de multimédia do Azure](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (pré-visualização)

Agora o Azure dos serviços de multimédia permite-lhe dinamicamente encriptar o seu HTTP Live transmissão (HLS) conteúdo com o Apple FairPlay. Também pode utilizar o serviço de entrega de licença AMS a FairPlay licenças para clientes. Para informações mais detalhadas, consulte o artigo [utilizar Azure dos serviços de multimédia para transmitir em fluxo seu HLS conteúdo protegido com Apple FairPlay ](media-services-protect-hls-with-fairplay.md).
  
##<a id="feb_changes16"></a>Lançamento de Fevereiro de 2016

A versão mais recente do Azure SDK de serviços de multimédia para .NET (trabalho 3.5.3) contém um fix erros relacionado Widevine. Foi o problema: não foi possível ser reutilizado AssetDeliveryPolicy para vários elementos encriptados com Widevine. Como parte desta correção de erros a propriedade seguinte foi adicionada para o SDK: **WidevineBaseLicenseAcquisitionUrl**.
    
    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
        
    };

##<a id="jan_changes_16"></a>Lançamento de Janeiro de 2016

Codificação de unidades reservadas cujo nome foi mudado para reduzir a ambiguidade com nomes de codificador.

As unidades de reservadas codificação Basic, padrão e Premium nome são alteradas para S1, S2, e S3 reservadas unidades de, respetivamente.  Os clientes que utilizam básicas RUs codificação hoje, irão ver S1 como etiqueta no Portal do Azure (e na fatura), enquanto padrão e Premium verá as etiquetas S2 e S3 respetivamente. 

##<a id="dec_changes_15"></a>Lançamento de Dezembro de 2015

A equipa do Azure SDK publicado uma nova versão do pacote do [Azure SDK para PHP](http://github.com/Azure/azure-sdk-for-php) que contém as atualizações e as funcionalidades novas para serviços de multimédia do Microsoft Azure. Em particular, o SDK de serviços de multimédia do Azure para PHP suporta agora as funcionalidades mais recentes do [conteúdo proteção](media-services-content-protection-overview.md) : encriptação dinâmica com AES e DRM (PlayReady e Widevine) com e sem restrições Token. Também suporta dimensionamento [Codificação de unidades](media-services-dotnet-encoding-units.md).

Para obter mais informações, consulte:

- O blogue do [Microsoft Azure multimédia serviços SDK para PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) .
- As seguintes [exemplos de código](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) para ajudar a iniciação rápida:
    - **vodworkflow_aes.php**: Este é um ficheiro PHP que mostra como utilizar encriptação AES de 128 de dinâmico e o serviço de entrega de chave. Baseia-se a amostra de .NET explicado [neste](media-services-protect-with-aes128.md) artigo.
    - **vodworkflow_aes.php**: Este é um ficheiro PHP que mostra como utilizar encriptação dinâmica PlayReady e serviço de entrega de licenças. Baseia-se a amostra de .NET explicado [neste](media-services-protect-with-drm.md) artigo.
    - **scale_encoding_units.php**: Este é um ficheiro PHP que mostra como dimensionar codificação unidade reservada.


##<a id="nov_changes_15"></a>Lançamento de Novembro de 2015

Azure dos serviços de multimédia oferece agora o serviço de entrega de licença do Google Widevine na nuvem. Para obter mais detalhes, consulte [Esta anúncio de nascimento de blogue](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Além disso, consulte o artigo [Este tutorial](media-services-protect-with-drm.md) e [GitHub repositório](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Note que os serviços de entrega de licença Widevine fornecidos pela serviços de multimédia do Azure é na pré-visualização. Para obter mais informações consulte o artigo [neste blogue](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Lançamento de Outubro de 2015

Serviços de multimédia Azure (MGA) está agora direto nos seguintes centros de dados: sul do Brasil, Índia oeste, Sul Índia e Índia Central. Agora pode utilizar o Portal clássica do Azure para [criar contas de serviço de multimédia](media-services-portal-create-account.md) e realizar várias tarefas descritas [aqui](https://azure.microsoft.com/documentation/services/media-services/). No entanto, o Live codificação não estiver ativado nestes centros de dados. Além disso, nem todos os tipos de codificação de unidades reservadas estão disponíveis nestes centros de dados.

- Sul Brasil: Padrão e de codificação reservadas unidades básicas estão apenas disponíveis
- Índia oeste, Sul Índia e Índia Central: estão disponíveis apenas codificação reservadas unidades básicas


##<a id="september_changes_15"></a>Lançamento de Setembro de 2015 

- AMS oferece agora a capacidade de proteger invés (VOD) e fluxos de Live com a tecnologia de Widevine Modular DRM. Pode utilizar os parceiros de serviços de entrega seguintes para ajudá-lo a entregar Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Para mais informações, consulte o artigo [neste blogue](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

    Pode utilizar [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (começando com a versão 3.5.1) ou REST API para configurar o seu AssetDeliveryConfiguration para utilizar Widevine.  

- AMS adicionado suporte para Apple ProRes vídeos. Agora pode carregar os ficheiros de vídeos de origem QuickTime que utilizem o Apple ProRes ou outros codecs. Para mais informações, consulte o artigo [neste blogue](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- Pode agora utilizar Media Encoder norma fazer sub recorte e direto extração de arquivo. Para mais informações, consulte o artigo [neste blogue](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- Foram efetuadas as seguintes atualizações de filtragem: 

    - Agora pode usar o formato da Apple HTTP Live transmissão (HLS) com o filtro só de áudio. Esta atualização permite-lhe remover registar só de áudio ao especificar (só de áudio = false) no URL.
    - Quando definir filtros para os seus ativos, tem agora capacidade para combinar múltiplos (até 3) filtros num único URL.

    Para obter mais informações consulte o artigo [neste](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogue.

- AMS suporta agora posso-molduras no HLS v4. Suporte de posso-Frame optimiza operações de funcionalidades de avançar e recuar. Por predefinição, todos os resultados de v4 HLS incluem posso-moldura lista pessoal (EXT-X-I-FRAME-STREAM-INF).
 
    Para obter mais informações consulte o artigo [neste](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogue.

##<a id="august_changes_15"></a>Lançamento de Agosto de 2015

- Azure SDK de serviços de multimédia para o lançamento Java V0.8.0 e novas amostras estão agora disponíveis. Para obter mais informações, consulte:

    - [Mensagem de blogue](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
    - [Repositório de amostras Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Actualização do Media Player Azure com suporte de sequência de múltiplos áudio. Para obter mais informações, consulte:
    - [Mensagem de blogue](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Lançamento de Julho de 2015

- Anunciar a disponibilidade geral de Media Encoder padrão. Para obter mais informações, consulte [Esta mensagem no blogue](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

    Media Encoder padrão utiliza predefinições descritas [nesta](http://go.microsoft.com/fwlink/?LinkId=618336) secção. Tenha em atenção que, quando utilizar uma configuração predefinida para 4k codifica, deve obter o tipo de unidade **Premium** reservados. Para mais informações, consulte o artigo [como codificação de escala](media-services-scale-media-processing-overview.md).
- Live legendas em tempo real com os serviços de multimédia do Azure e Player. Para obter mais informações, consulte [Esta mensagem de blogue](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###<a name="media-services-net-sdk-updates"></a>Serviços de multimédia .NET SDK atualizações

Azure multimédia serviços .NET SDK está agora a versão 3.4.0.0. A seguinte funcionalidade foi adicionada nesta versão:  

- Implementado suporte para o arquivo direto. Tenha em atenção que não é possível transferir um ativo que contém um arquivo de direto.
- Obter suporte implementado para filtros dinâmicos.
- Funcionalidade implementada que permite aos utilizadores manter o contentor de armazenamento ao eliminar ativo.
- Correções de erros relacionados com a repetir políticas de canais.
- Ativado **o fluxo de trabalho do Media Encoder Premium**.

##<a id="june_changes_15"></a>Lançamento de Junho de 2015

###<a name="media-services-net-sdk-updates"></a>Serviços de multimédia .NET SDK atualizações

Azure multimédia serviços .NET SDK está agora a versão 3.3.0.0. A seguinte funcionalidade foi adicionada nesta versão:  

- suporte para especificação de deteção de ligar OpenId
- suporte de processamento de sobreposição de teclas no lado do fornecedor de identidade. 

Se estiver a utilizar um fornecedor de identidades que expõe OpenID ligar documentos de deteção (tal como os seguintes fornecedores: Azure Active Directory, Google, Salesforce), pode indicar dos serviços de multimédia do Azure para obter as chaves de assinatura para especificação de deteção de ligação de validação do token JWT a partir do OpenID. 

Para mais informações, consulte o artigo [Utilizar Json as teclas Web a partir das OpenID ligar especificações de deteção para trabalhar com autenticação de token JWT nos serviços de multimédia do Azure](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Versão de Maio de 2015

Anunciar o as seguintes novas funcionalidades:

- [Uma pré-visualização do Live codificação com dos serviços de multimédia](media-services-manage-live-encoder-enabled-channels.md)
- [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)
- [Uma pré-visualização de processador de multimédia Hyperlapse de multimédia do Azure](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Lançamento de Abril de 2015

 ###<a name="general-media-services-updates"></a>Atualizações de serviços de multimédia de geral

- [Anunciar o Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- Começar com 2.10 no resto de serviços de multimédia, canais que estão configurados para ingerir esta última um protocolo RTMP, são criadas com principal e secundário ingerir esta última URLs. Para obter mais informações, consulte o artigo [canal ingerir esta última configurações](media-services-live-streaming-with-onprem-encoders.md#channel_input)
- Atualizações de multimédia indexador Azure
- Suporte para o idioma espanhol
- Novo formato de xml de configuração

Para obter mais informações consulte o artigo [neste blogue](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###<a name="media-services-net-sdk-updates"></a>Serviços de multimédia .NET SDK atualizações

Azure multimédia serviços .NET SDK está agora a versão 3.2.0.0.

Seguem-se alguns dos opostas actualizações cliente:

- **Força de alteração**: alteradas **TokenRestrictionTemplate.Issuer** e **TokenRestrictionTemplate.Audience** para ser de um tipo de cadeia.
- Atualizações relacionadas com a criação personalizada repetir políticas.
- Correções de erros relacionados com a carregar/transferência de ficheiros.
- Agora, a classe de **MediaServicesCredentials** aceita ponto final de controlo de acesso principais e secundários para autenticar.



##<a id="march_changes_15"></a>Lançamento de Março de 2015

### <a name="general-media-services-updates"></a>Atualizações de serviços de multimédia de geral

- Dos serviços de multimédia fornece agora integração de Azure CDN. Para suportar a integração, a propriedade **CdnEnabled** foi adicionada ao **StreamingEndpoint**.  **CdnEnabled** podem ser utilizados com REST APIs começando com a versão 2.9 (para obter mais informações, consulte o artigo [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)).  **CdnEnabled** podem ser utilizados com .NET SDK começando com a versão 3.1.0.2 (para obter mais informações, consulte o artigo [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Anúncio de **fluxo de trabalho do Media Encoder Premium**. Para mais informações, consulte o artigo [Introdução ao Premium codificação nos serviços de multimédia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Lançamento de Fevereiro de 2015

### <a name="general-media-services-updates"></a>Atualizações de serviços de multimédia de geral

Serviços de multimédia REST API é agora versão 2.9. Iniciar com esta versão, pode ativar a integração com os pontos finais de transmissão o Azure CDN. Para mais informações, consulte o artigo [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Lançamento de Janeiro de 2015

### <a name="general-media-services-updates"></a>Atualizações de serviços de multimédia de geral

Anúncio de geral disponibilidade das versões (DG) de proteção de conteúdo com encriptação dinâmica. Para mais informações, consulte o artigo [dos serviços de multimédia do Azure melhora a transmissão de segurança com a tecnologia de disponibilidade DRM geral](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###<a name="media-services-net-sdk-updates"></a>Serviços de multimédia .NET SDK atualizações

Azure multimédia serviços .NET SDK está agora a versão 3.1.0.1.

Nesta versão marcado o construtor de Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate predefinido como obsoleto. O construtor de novo leva TokenType como um argumento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Lançamento de Dezembro de 2014

###<a name="general-media-services-updates"></a>Atualizações de serviços de multimédia de geral

- Algumas atualizações e novas funcionalidades foram adicionadas ao processador de multimédia do Azure indexador. Para mais informações, consulte o artigo [Notas de lançamento do Azure multimédia indexador versão 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Adicionado uma nova API REST que permite-lhe atualizar a codificação reservada unidades: [EncodingReservedUnitType com restantes](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- CORS adicionados o suporte para o serviço de entrega chave.
- Melhorias de desempenho de consultar opções de política de autorização estar concluídas.
- No Centro de dados da China, é agora o [URL de entrega de chave](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) por cliente (tal como nos outros centros de dados).
- Duração de destino do automática HLS adicionada. Quando efetuar a transmissão em direto, HLS está sempre compactado dinamicamente. Por predefinição, dos serviços de multimédia calcula automaticamente HLS segmento embalagem rácio (FragmentsPerSegment) com base no intervalo de fotograma-chave (KeyFrameInterval), também conhecida como grupo de imagens – GOP, que é recebido a partir do codificador direto. Para mais informações, consulte o artigo [trabalhar com a transmissão do Azure multimédia Serviços Live].
 
###<a name="media-services-net-sdk-updates"></a>Serviços de multimédia .NET SDK atualizações

- [Azure multimédia serviços .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) está agora a versão 3.1.0.0.
- Atualizado a dependência de .net SDK para .NET Framework 4,5.
- Adicionada uma nova API permite-lhe atualizar a codificação de unidades reservadas. Para mais informações, consulte o artigo [atualizar o tipo de unidade reservadas e aumentar RUs codificação utilizando o .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Adicionado JWT (JSON Web Token) o suporte para autenticação de token. Para mais informações, consulte o artigo [JWT autenticação token dos serviços de multimédia do Azure e encriptação dinâmicos](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Adicionado relativos desvios para BeginDate e ExpirationDate no modelo de licença do PlayReady.


##<a id="november_changes_14"></a>Versão de Novembro de 2014

- Dos serviços de multimédia agora permite-lhe ingerir esta última uma live conteúdo suaves transmissão (FMP4) através de uma ligação SSL. Para ingerir esta última através de SSL, certifique-se atualizar o URL de ingest por HTTPS.  Para mais informações sobre live transmissão, consulte o artigo [trabalhar com a transmissão do Azure multimédia Serviços Live].
- Tenha em atenção que neste momento, possam ingerir esta última uma sequência de direto RTMP através de uma ligação SSL.
- Também pode passe os seus conteúdos através de uma ligação SSL. Para fazer isto, certifique-se das que transmissão URLs começam por HTTPS.
- Tenha em atenção que é possível apenas transmitir necessidade através de SSL se o ponto final de transmissão a partir do qual distribuir o conteúdo que foi criado após 10 de Setembro de 2014. Se o seu URLs transmissão sejam baseiam os pontos finais da transmissão criados depois de Setembro de 10, o URL contém "streaming.mediaservices.windows.net" (o novo formato). Transmissão URLs que contêm "origin.mediaservices.windows.net" (o formato antigo) não suportam SSL. Se o seu URL é no formato antigo e pretende conseguir transmitir através de SSL, [criar um novo ponto final transmissão](media-services-portal-manage-streaming-endpoints.md)em fluxo. Utilize criadas com baseados no novo ponto final de transmissão de URLs para transmitir em fluxo conteúdo através de SSL.

##<a id="october_changes_14"></a>Versão de Outubro de 2014

### <a id="new_encoder_release"></a>Lançamento de codificador dos serviços de multimédia

Anunciar o a nova versão do Azure de serviços de multimédia Media Encoder. Com as mais recentes descodificador de multimédia do Azure apenas são cobrados por GB de saída, mas caso contrário, o novo codificador é compatível com o anterior codificador de funcionalidade. Para obter mais informações [Detalhes de preços de serviços de multimédia]).

### <a id="oct_sdk"></a>.NET SDK dos serviços de multimédia 

SDK de serviços de multimédia para as extensões de .NET está agora a versão 2.0.0.3.

SDK de serviços de multimédia para .NET está agora a versão 3.0.0.8.

Foram efetuadas as seguintes alterações:

- Refactorização nas categorias de política de repetir.
- Adicionar a cadeia de agente do utilizador a cabeçalhos de pedido de http.
- Adicionar nuget restaurar compilação passo.
- Corrigir testes de cenário para utilizar x509 orientação do diapositivo notas a partir do repositório.
- Validar definições ao atualizar o canal e transmissão de fim.
 

### <a name="new-github-repository-to-host-media-services-samples"></a>Novo GitHub repositório aos anfitrião dos serviços de multimédia de amostras

Amostras estão localizadas no [repositório de GitHub de amostras dos serviços de multimédia do Azure](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Versão de Setembro de 2014

RESTO de serviços de multimédia metadados está agora a versão 2.7. Para mais informações sobre as atualizações mais recentes do resto, consulte o artigo [Referência da API do resto do Azure multimédia serviços].

SDK de serviços de multimédia para .NET é agora versão 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Força de alterações

* **Origem** nome foi mudado para [StreamingEndpoint].
* Uma alteração no comportamento predefinido quando utilizar o **Portal clássica Azure** codificar e, em seguida, publicar MP4 ficheiros.

Anteriormente, quando através do Portal clássica Azure para publicar um único ficheiro MP4 vídeo de ativos um URL de SA deverá ser criada (SA URLs permitem-lhe transferir o vídeo a partir de um armazenamento de BLOBs). Atualmente, quando utiliza o Portal clássica Azure codificar e, em seguida, publicar um activo de vídeo MP4 único ficheiro, o URL gerado aponta para dos serviços de multimédia do Azure transmissão ponto final.  Esta alteração não afeta MP4 vídeos que carregou para dos serviços de multimédia diretamente e publicados sem a ser codificado dos serviços de multimédia do Azure.

Atualmente, tem duas opções seguintes para resolver o problema.

* Ativar a transmissão de unidades e utilize embalagem dinâmica para transmitir em fluxo activo. mp4 como uma apresentação de transmissão suave.

* Crie um url de SA para transferir (ou gradualmente reproduzir) a. mp4. Para mais informações sobre como criar um localizador de SA, consulte o artigo [Realizar conteúdo].


### <a id="sept_14_GA_changes"></a>Novos funcionalidades/cenários que fazem parte da edição das versões DG

* **Processador de multimédia do indexador**. Para obter mais informações, consulte [a indexação ficheiros de multimédia com indexador de multimédia do Azure].

* A entidade de [StreamingEndpoint] agora permite-lhe adicionar nomes de domínio personalizado (anfitrião).

    Um nome de domínio personalizado ser utilizado como o nome do ponto final de sequência dos serviços de multimédia, tem de adicionar nomes de anfitrião personalizado para o ponto final transmissão. Utilize o suporte de dados serviços REST APIs ou .NET SDK para adicionar nomes de anfitrião personalizado.
    
    As seguintes considerações aplicam-se:
    
    * Tem de ter o que é o proprietário do nome do domínio personalizado.
    
    * O que é o proprietário do nome de domínio deve ser validado através dos serviços de multimédia do Azure. Para validar o domínio, crie um CName que mapas <MediaServicesAccountId>.<parent domain> para verifydns. < zona de dns de mediaservices >. 
    
    * Tem de criar outro CName que mapeie o nome do anfitrião personalizado (por exemplo, sports.contoso.com) para o nome de anfitrião do StreamingEndpont de serviços de multimédia (por exemplo, amstest.streaming.mediaservices.windows.net).


    Para mais informações, consulte a propriedade **CustomHostNames** no tópico [StreamingEndpoint] .

### <a id="sept_14_preview_changes"></a>Novos funcionalidades/cenários que fazem parte da versão de pré-visualização público

* Transmissão de pré-visualização dinâmica. Para mais informações, consulte o artigo [trabalhar com a transmissão do Azure multimédia Serviços Live].

* Serviço de entrega chave. Para mais informações, consulte o artigo [utilizar encriptação dinâmico da AES de 128 e serviço de entrega de chave].

* Encriptação AES de dinâmica. Para mais informações, consulte o artigo [utilizar encriptação dinâmico da AES de 128 e serviço de entrega de chave].

* Serviço de entrega de PlayReady licença. Para mais informações, consulte o artigo [utilizar encriptação dinâmico da PlayReady e serviço de entrega de licenças].

* Encriptação PlayReady dinâmicos. Para mais informações, consulte o artigo [utilizar encriptação dinâmico da PlayReady e serviço de entrega de licenças].

* Modelo de licença PlayReady de serviços de multimédia. Para mais informações, consulte o artigo [Multimédia serviços PlayReady licença descrição geral dos modelos].

* Armazenamento de transmissão encriptado elementos. Para mais informações, consulte o artigo [Encriptados conteúdo da armazenamento do transmissão].

##<a id="august_changes_14"></a>Lançamento de Agosto de 2014

Quando codificar um ativo, um ativo de saída é produzido após a conclusão da tarefa de codificação de. Até neste lançamento, descodificador de serviços de multimédia do Azure produzidos metadados sobre os elementos de saída. Também começando nesta versão codificador gera metadados sobre os elementos de entrada. Para mais informações, consulte os tópicos de [Metadados de entrada] e [Saída metadados] .


##<a id="july_changes_14"></a>Versão de Julho de 2014

As correções de erros seguintes foram efetuadas para o Gestor de serviços de multimédia do Azure e EncDec:

* Apenas áudio é reproduzido novamente quando transmuxing um activo arquivo direto ao HTTP Live Streaming – este erro foi corrigido e agora são reproduzidos áudio e vídeo.

* Quando embalagem um ativo para HTTP Live transmissão e encriptação de envelope AES de 128-bit, as sequências embaladas não são reproduzidos em dispositivos Android – este erro foi corrigido e a sequência de embalado for reproduzido em dispositivos Android que suportam HTTP Live transmissão.

##<a id="may_changes_14"></a>Versão de Maio de 2014

### <a id="may_14_changes"></a>Atualizações de serviços de multimédia de geral

Agora pode usar [Embalagem dinâmico] para v3 de HTTP Live transmissão (HLS) da cadeia. Para transmitir em fluxo HLS v3, adicione o seguinte formato ao caminho do localizador de origem: * .ism/manifest(format=m3u8-aapl-v3). Para mais informações, consulte o [blogue do Nick Drouin].

Embalagem dinâmica agora também suporta faz HLS (v3 e v4) encriptados com PlayReady com base em sequência suaves estática encriptados com PlayReady. Para obter informações sobre como encriptar transmissão suaves com PlayReady, consulte [proteger sequência suaves com PlayReady].

### <a name="may_14_donnet_changes"></a>Serviços de multimédia .NET SDK atualizações

As seguintes melhorias estão incluídas na versão de multimédia serviços .NET SDK 3.0.0.5:

* Melhor velocidade e resistência para carregar/transferir elementos de multimédia.

* Melhorias na repetir lógica e transitória de exceções: 

    * Lógica de deteção e volte a tentar erro breves foram melhorada para exceções que problemas são causadas pela consulta, guardar as alterações, ao carregar ou transferir ficheiros. 
    
    * Quando receber exceções da Web (por exemplo, durante um pedido de token ACS), vai notar que erros fatais estão a falhar mais rápido agora.

Para mais informações, consulte o artigo [Lógica repetir no SDK de serviços de multimédia para .NET].

##<a id="april_changes_14"></a>Lançamento de codificador de Abril de 2014

### <a name="april_14_enocer_changes"></a>Atualizações de codificador dos serviços de multimédia

* Suporte adicionada para ingesting AVI os ficheiros criados utilizando o editor de não linear relva Valley EDIUS, onde o vídeo é ligeiramente comprimidos utilizando o Centro de Valley relva/HQX codec. Para mais informações, consulte o artigo [Relva do vale do anuncia EDIUS 7 transmissão através da nuvem].

* Obter suporte adicionada para especificar a Convenção de nomenclatura para os ficheiros produzidos pelo Media Encoder. Para mais informações, consulte o artigo [Controlar multimédia serviço codificador saída nomes de ficheiro].

* Obter suporte adicionada para as Sobreposições de vídeos e/ou áudio. Para mais informações, consulte o artigo [Criar sobreposições].

* Obter suporte adicionada para Lombada em conjunto vários segmentos de vídeos. Para mais informações, consulte o artigo [Lombada segmentos de vídeo].

* Fixo um erro relacionado com transcodificação de MP4s onde o áudio ter sido codificado com áudio MPEG-1 layer 3 (também conhecidos por MP3).


##<a id="jan_feb_changes_14"></a>Janeiro/Fevereiro de 2014 lançamentos

### <a name="jan_fab_14_donnet_changes"></a>Serviços de multimédia do Azure .NET SDK 3.0.0.1, 3.0.0.2 e 3.0.0.3

As alterações no 3.0.0.1 e 3.0.0.2 incluem:

* Fixos problemas relacionados com a utilização de consultas LINQ com OrdenarPor demonstrações.

* Teste de divisão soluções no [GitHub] numa unidade com base em testes e demonstrativos testes.

Para obter mais detalhes sobre as alterações, consulte o artigo: [Azure multimédia serviços .NET SDK 3.0.0.1 e 3.0.0.2 liberta].

As seguintes alterações foram efetuadas no 3.0.0.3:

* Dependências de armazenamento Azure atualizado para utilizar a versão 3.0.3.0. 

* Resolvi o problema de compatibilidade com versões anteriores para 3.0. *.* versões. 


##<a id="december_changes_13"></a>Lançamento de Dezembro de 2013

### <a name="dec_13_donnet_changes"></a>Serviços de multimédia do Azure .NET SDK 3.0.0.0

>[AZURE.NOTE] 3.0.x.x lançamentos não são compatíveis com versões anteriores com 2.4.x.x lançamentos.

A versão mais recente do SDK de serviços de multimédia está agora 3.0.0.0. Pode transferir o pacote mais recente do Nuget ou obtenha os bits do [GitHub].

Começar com o SDK de serviços de multimédia versão 3.0.0.0, pode reutilizar os tokens [Azure Active Directory Access serviço de controlo (ACS)] . Para mais informações, consulte a secção "Reutilizar controlo serviço Tokens de acesso" no tópico [para ligar dos serviços de multimédia com o SDK de serviços de multimédia para .NET] .

### <a name="dec_13_donnet_ext_changes"></a>Serviços de multimédia do Azure .NET SDK extensões 2.0.0.0

Extensões de SDK .NET dos serviços de Azure multimédia é um conjunto de métodos de extensão e funções auxiliares que tornam mais fácil desenvolver com os serviços de multimédia do Azure e simplificar o seu código. Pode obter os bits mais recentes do [Azure multimédia serviços .NET SDK extensões].

##<a id="november_changes_13"></a>Lançamento de Novembro de 2013

### <a name="nov_13_donnet_changes"></a>Serviços de multimédia do Azure .NET SDK alterações

Iniciar com esta versão, o SDK de serviços de multimédia para .NET trata breves erros que podem ocorrer ao efetuar chamadas para a camada de serviços de multimédia REST API.

##<a id="august_changes_13"></a>Lançamento de Agosto de 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets do PowerShell de serviços de multimédia incluído nas ferramentas de Sdk do Azure

Os seguintes cmdlets do PowerShell dos serviços de multimédia estão agora incluídos nas [Ferramentas de sdk azure].

* Get-AzureMediaServices 

    Por exemplo, `Get-AzureMediaServicesAccount`.

* Novo AzureMediaServicesAccount 

    Por exemplo, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* Novo AzureMediaServicesKey 

    Por exemplo, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Remover AzureMediaServicesAccount 

    Por exemplo, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Lançamento de Junho de 2013

### <a name="june_13_general_changes"></a>Alterações do Azure dos serviços de multimédia

As alterações mencionadas nesta secção são actualizações incluídas as versões dos serviços de multimédia de Junho de 2013.

* Capacidade de ligar a várias contas de armazenamento para uma conta de serviço de multimédia. 

    StorageAccount
    
    Asset.StorageAccountName e Asset.StorageAccount

* Capacidade de atualizar Job.Priority. 

* Notificação relacionadas com entidades e propriedades: 

    JobNotificationSubscription
    
    NotificationEndPoint
    
    Tarefa

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Alterações de serviços de multimédia .NET SDK Azure

As seguintes alterações estão incluídas de Junho de 2013 disponibilização de SDK de serviços de multimédia. O SDK de serviços de multimédia mais recente está disponível no GitHub.

* Começar com a versão 2.3.0.0, o suporta SDK de serviços de multimédia ligar armazenamento várias contas a uma conta dos serviços de multimédia. As seguintes APIs suportam esta funcionalidade:
    
    O tipo de IStorageAccount.
    
    A propriedade Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
    
    A propriedade StorageAccount.
    
    A propriedade StorageAccountName.
    
    Para mais informações, consulte o artigo [Gerir elementos de serviços de multimédia em várias contas de armazenamento].

* APIs relacionadas com notificação. Começar com a versão 2.2.0.0 que tem a capacidade para ouvir notificações de armazenamento do Azure fila de espera. Para obter mais informações, consulte [Processamento de notificações de tarefa de serviços de multimédia].
    
    A propriedade Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
    
    O tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dependência de cliente do armazenamento Azure SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dependência de OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Lançamento de Dezembro de 2012

### <a name="dec_12_dotnet_changes"></a>Alterações de serviços de multimédia .NET SDK Azure

* IntelliSense: Adicionados em falta documentação Intellisense para muitos tipos de.

* Microsoft.Practices.TransientFaultHandling.Core: Fixo um problema em que o SDK tinha ainda uma dependência de uma versão antiga desta assemblagem. Agora o SDK referencia a versão 5.1.1209.1 desta assemblagem.

Correções para problemas que se encontram de Novembro de 2012 SDK:

* IAsset.Locators.Count: Esta contagem é agora corretamente comunicada em novas IAsset interfaces após todas as Locator terem sido eliminado.

* IAssetFile.ContentFileSize: Este valor é agora corretamente definido após um carregamento pelo IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize: Esta propriedade agora pode ser definida ao criar um ficheiro de elementos. Estava anteriormente só de leitura.

* IAssetFile.Upload(filepath): Fixo um problema onde este método de carregamento síncrono foi deitar a seguinte mensagem de erro ao carregar vários ficheiros para o elemento. Erro: "servidor falhou para autenticar o pedido. Certifique-se que o valor do cabeçalho de autorização está formatado corretamente, incluindo a assinatura."

* IAssetFile.UploadAsync: Fixo um problema onde mais do que 5 ficheiros podem ser carregados em simultâneo.

* IAssetFile.UploadProgressChanged: Este evento agora é fornecido pelo SDK.

* IAssetFile.DownloadAsync (cadeia, BlobTransferClient, ILocator, CancellationToken): Sobrecarga este método é fornecida agora.

* IAssetFile.DownloadAsync: Fixo um problema onde mais do que 5 ficheiros podem ser transferidos em simultâneo.

* IAssetFile.Delete(): Fixo um problema onde eliminar chamada pode gerar uma exceção se foi carregado qualquer ficheiro para o IAssetFile.

* Tarefas: Fixo um problema onde a interligação de um "MP4 a tarefa de sequências suaves" com uma "PlayReady proteção tarefa" com um modelo de tarefa não pretende criar todas as tarefas de todo.

* EncryptionUtils.GetCertificateFromStore(): Este método já não inicia uma exceção de referência nula devido a um falhas encontra o certificado com base em problemas de configuração de certificado.

##<a id="november_changes_12"></a>Lançamento de Novembro de 2012

As alterações mencionadas nesta secção foram atualizações de Novembro de 2012 (versão 2.0.0.0) incluídas SDK. Estas alterações precisem de qualquer código escrito para a pré-visualização de Junho de 2012 SDK solte ser modificadas ou reprogramados.

* Elementos
    
    IAsset.Create(assetName) é a função de criação de elementos de apenas. IAsset.Create já não carrega ficheiros como parte da chamada método. Utilize IAssetFile para carregar.
    
    O método de IAsset.Publish e o valor de enumeração AssetState.Publish foram removidos do SDK Services. Qualquer código que depende este valor tem de ser escrito novamente.

* FileInfo

    Esta classe foi removida e substituída pelo IAssetFile.

    IAssetFiles

    IAssetFile substitui FileInfo e tem um comportamento diferente. Para utilizá-la, criar uma instância o objeto IAssetFiles, seguido de um carregamento de ficheiro que quer utilizar o SDK de serviços de multimédia ou o SDK do armazenamento Azure. Podem ser utilizados os overloads IAssetFile.Upload seguintes:

    * IAssetFile.Upload(filePath): Um síncrono método que bloqueia o tópico e só é recomendado quando um único ficheiro a carregar.
    
    * IAssetFile.UploadAsync (filePath, blobTransferClient, locator, cancellationToken): um método de assíncrono. Este é o mecanismo carregamento preferido. 

    Erros conhecidos: utilizar o cancellationToken facto irá cancelar o carregamento; No entanto, o estado de cancelamento das tarefas pode ser qualquer um de um número de Estados-membros. Tem corretamente capturas e processar exceções.

* Locator
    
    As versões de origem específicas foram removidas. O contexto SA específicas. Locators.CreateSasLocator (ativo, accessPolicy) serão marcadas como preterido ou removidas por GA. Consulte a secção Locator em nova funcionalidade para o comportamento de atualizado.


##<a id="june_changes_12"></a>Versão de pré-visualização de Junho de 2012

A seguinte funcionalidade foi nova na versão de Novembro do SDK.

* Eliminação de entidades

    IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey objetos são eliminados agora ao nível do objeto, ou seja, IObject.Delete() em vez de que exige um eliminar na coleção de que está cloudMediaContext.ObjCollection.Delete(objInstance).

* Locator

    Locator agora deve ser criado utilizando o método de CreateLocator e utilize os valores de enumeração LocatorType.SAS ou LocatorType.OnDemandOrigin como um argumento para o tipo específico de locator que pretende criar.

    Foram adicionadas novas propriedades Locator para facilitar a obter URIs utilizáveis para o seu conteúdo. Esta reformulação de Locator deveriam fornecem mais flexibilidade para futura expansão de terceiros e aumentar facilidade de utilização para as aplicações de cliente de multimédia.

* Suporte de método assíncrona

    Suporte assíncrona foi adicionado a todos os métodos.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Fórum MSDN de serviços de multimédia do Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referência da API REST de serviços de multimédia do Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Detalhes do preços de serviços de multimédia]: http://azure.microsoft.com/pricing/details/media-services/
[Entrada de metadados]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadados de saída]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Distribuir conteúdo]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indexação ficheiros de multimédia com indexador de multimédia do Microsoft Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Trabalhar com os serviços de multimédia do Microsoft Azure Live transmissão]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Utilizar encriptação AES de 128 de dinâmica e o serviço de entrega chave]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Utilizar encriptação PlayReady dinâmicos e serviço de entrega de licença]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Descrição geral dos modelos PlayReady licença de serviços de multimédia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Armazenamento de transmissão encriptados conteúdo]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[Embalagem dinâmica]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blogue de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Proteger sequência suave com PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Volte a tentar lógica nos serviços de multimédia SDK para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Relva Valley anuncia EDIUS 7 transmissão através da nuvem]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Multimédia controlar nomes de ficheiro do codificador saída do serviço]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Criar sobreposições]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Lombada segmentos de vídeos]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Versões de multimédia serviços .NET SDK 3.0.0.1 e 3.0.0.2 Azure]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Serviço de controlo de acesso do Azure Active Directory (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Ligar a dos serviços de multimédia com os serviços de multimédia SDK para .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[.NET SDK extensões de serviços de multimédia do Azure]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[ferramentas do sdk do Azure]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Gestão de multimédia dos serviços de activos em várias contas de armazenamento]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Tratamento de multimédia dos serviços de notificações de tarefa]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 
