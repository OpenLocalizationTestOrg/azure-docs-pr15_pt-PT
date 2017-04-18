<properties 
    pageTitle="Inserir anúncios do lado do cliente | Microsoft Azure" 
    description="Este tópico mostra como inserir anúncios do lado do cliente." 
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


#<a name="inserting-ads-on-the-client-side"></a>Inserir anúncios do lado do cliente

Este tópico contém informações sobre como inserir vários tipos de anúncios no lado do cliente.

Para obter informações sobre o suporte de legendagem e ad fechado no Live transmissão vídeos, consulte o artigo [suportadas legendas fechadas e padrões de inserção de Ad](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

>[AZURE.NOTE] Azure Media Player atualmente não suporta anúncios.

##<a id="insert_ads_into_media"></a>Inserir anúncios nos ficheiros de multimédia

Azure dos serviços de multimédia fornece suporte para a inserção de ad através da plataforma do Windows Media: Player quadros. Quadros de leitor com suporte de ad estão disponíveis para dispositivos Windows 8, Windows Phone 8, Silverlight e iOS. Cada quadro de leitor contém o código de exemplo que mostra como implementar uma aplicação do leitor. Existem três tipos diferentes de anúncios que pode inserir na sua lista: multimédia.

- **Linear** – anúncios de moldura completa que interromper o vídeo principal.
- **Nonlinear** – anúncios de sobreposição que são apresentados como estiver a reproduzir o vídeo principal, normalmente, um logótipo ou outra imagem estática colocado dentro do leitor.
- **Assistente de** – anúncios que são apresentadas fora do leitor.

Anúncios podem ser colocados em qualquer ponto na linha de tempo o vídeo principal. Tem de indicar ao Media player quando reproduzir o ad e que anúncios para reproduzir. Isto é feito utilizando um conjunto de ficheiros baseado em XML padrão: vídeo Ad serviço modelo (VAST), Digital vídeo vários Ad lista pessoal (VMAP), modelo de Sequencing Abstratos multimédia (LATA) e Digital vídeo Player Ad Interface definição (VPAID). Ficheiros VASTOS especificam que anúncios para apresentar. Ficheiros VMAP especificam quando reproduzir vários anúncios e contêm XML grande. Ficheiros de LATA são outra forma de anúncios de sequência que também pode conter XML grande. Ficheiros VPAID definem uma interface entre o leitor de vídeo e o ad ou servidor dos ad.

Cada quadro de leitor funciona de forma diferente e cada será coberta na sua própria tópico. Este tópico irá descrevem os mecanismos básicos utilizados para inserir anúncios. Aplicações de leitor de vídeo pedem anúncios de um servidor do ad. O servidor do ad pode responder um número das formas:

- Devolver um ficheiro vasto
- Devolver um ficheiro VMAP (com VAST incorporado)
- Devolver um ficheiro de LATA (com VAST incorporado)
- Devolver um ficheiro vasto com anúncios VPAID

 
###<a name="using-a-video-ad-service-template-vast-file"></a>Utilizar um ficheiro de modelo (VAST) do serviço de vídeo Ad

Um ficheiro vasto Especifica que ad ou anúncios para apresentar. O XML seguinte é um exemplo de um ficheiro vasto para um ad linear:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
    
O ad linear é descrito pela **<Linear>** elemento. Especifica a duração do ad, eventos de controlo, clique em através de, clique em controlo e um número de **<MediaFile>** elementos. Eventos de controlo forem especificados dentro de **<TrackingEvents>** elemento e permitir que um servidor do ad para controlar várias eventos que ocorram enquanto vê o ad. Neste caso iniciar, ponto intermédio, concluído e expanda eventos são registados. O evento de início ocorre quando o ad é apresentado. O evento de ponto intermédio ocorre quando, pelo menos, visualizou 50% da linha de tempo de ad. O evento concluído ocorre quando o ad tem executar para o fim. O evento de expandir ocorre quando o utilizador expande o leitor de vídeo para ecrã inteiro. Clickthroughs são especificadas com um **<ClickThrough>** elemento dentro de uma **<VideoClicks>** elemento e especifica um URI a um recurso para apresentar quando o utilizador clica de ad. ClickTracking especificado num **<ClickTracking>** elemento, também dentro de **<VideoClicks>** elemento e especifica um recurso de controlo para o leitor pedir quando o utilizador clica de ad. O **<MediaFile>** elementos especificam informações sobre uma determinada codificação de um anúncio. Quando existe mais do que um **<MediaFile>** elemento, o leitor de vídeo pode escolher a codificação melhor para a plataforma. 

Anúncios lineares podem ser apresentados numa ordem específica. Para executar esta tarefa, adicione adicionais <Ad> elementos para o VAST ficheiro e especificar a ordem utilizando o atributo sequência. O exemplo seguinte ilustra esta situação:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
    
Anúncios não lineares são especificados num <Creative> elemento também. Mostra o exemplo seguinte um <Creative> elemento que descreve um ad não linear.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>

 
O **<NonLinearAds>** elemento pode conter um ou mais **<NonLinear>** elementos, cada uma das quais pode descrever um ad não linear. O **<NonLinear>** elemento Especifica o recurso para o suporte linear ad. O recurso pode ser uma **<StaticResouce>**, um **<IFrameResource>**, ou uma **<HTMLResouce>**.**<StaticResource>** Descreve um recurso que não sejam HTML e define um atributo creativeType que especifica como é apresentado o recurso:

Imagem/gif, jpeg/imagem, imagem/png – o recurso é apresentado numa HTML **<img>** etiqueta.

Aplicação/x-javascript – o recurso é apresentado numa tag HTML <**script**>.

Aplicação/x-shockwave-flash – o recurso é apresentado num Flash Player.

**<IFrameResource>**Descreve um recurso HTML que pode ser apresentado numa IFrame. **<HTMLResource>**Descreve uma peça de código HTML que pode ser inserido numa página web. **<TrackingEvents>**especificar eventos de controlo e o URI para pedir quando ocorre o evento. Neste exemplo são registados os eventos acceptInvitation e fechar. Para mais informações sobre o **<NonLinearAds>** elemento e sites secundários, consulte o artigo IAB.NET/VAST. Tenha em atenção que a **<TrackingEvents>** elemento é localizado dentro de** <NonLinearAds> ** elemento em vez do **<NonLinear>** elemento.

Anúncios acompanha são definidos dentro de uma <CompanionAds> elemento. O <CompanionAds> elemento pode conter um ou mais <Companion> elementos. Cada <Companion> elemento descreve ad um assistente e pode conter um <StaticResource>, <IFrameResource>, ou <HTMLResource> que estão especificado da mesma forma, tal como um ad não linear. Um ficheiro vasto pode conter várias anúncios de acompanha e a aplicação de leitor pode selecionar o ad mais adequado para apresentar. Para mais informações sobre VAST, consulte o artigo [VASTA 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

###<a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Utilizar um vídeo Digital um ficheiro de lista pessoal (VMAP) Ad múltiplo

Um ficheiro VMAP permite-lhe especificar quando ocorrem ad quebras, quanto tempo cada quebra é, quantas anúncios podem ser apresentados dentro de uma quebra de e que tipos de anúncios poderão ser apresentada durante uma quebra. A seguinte num ficheiro VMAP exemplo que define uma quebra de ad única:
    
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
     
Um ficheiro VMAP começa com um <VMAP> elemento que contém um ou mais <AdBreak> elementos, cada uma quebra de ad definir. Cada quebra ad Especifica um tipo de quebra, quebra ID e desvio de tempo. O atributo breakType Especifica o tipo de ad que pode ser reproduzido durante a quebra: linear, não linear, ou apresentar. Apresentar o mapa de anúncios para anúncios acompanha VASTA. Mais do que um tipo de ad pode ser especificado numa lista separados por vírgulas (sem espaços). O breakID é um identificador opcional para o ad. O timeOffset Especifica quando o ad deve ser apresentado. Pode ser especificado de uma das seguintes formas:

1. Hora – no formato de ss ou hh:mm:ss.mmm onde .mmm é milissegundos. O valor deste atributo Especifica a hora desde o início da linha cronológica vídeo para o início da quebra de ad.
1. Percentagem – no formato de n % sempre que n corresponde à percentagem da linha cronológica vídeo para reproduzir antes de o reproduzir o ad
1. Início/fim – Especifica que deve ser apresentado um ad antes ou depois do vídeo tenha sido apresentado
1. Posicione – Especifica a ordem das quebras de ad quando a temporização de todas as quebras de ad é desconhecido, tal como nos transmissão direto. A ordem de cada quebra de ad é especificada no formato de #n onde n é um número inteiro 1 ou maior. 1 se trata o ad deve ser reproduzido à primeira oportunidade, 2 se trata o ad que deve ser reproduzido na segunda oportunidade e assim sucessivamente.

Dentro do elemento <**AdBreak**> pode ser um elemento de <**AdSource**>. O elemento <**AdSource**> contém os seguintes atributos:

1. ID da – Especifica um identificador para a origem do ad
1. allowMultipleAds – um valor booleano que especifica se a publicidade vários pode ser apresentadas durante a quebra de ad
1. followRedirects – um valor booleano opcional que especifica se o leitor de vídeo deve respeitar redireciona dentro de uma resposta de ad

O elemento de <**AdSource**> fornece o leitor uma resposta de ad inline ou uma referência a uma resposta de ad. Pode conter um dos seguintes elementos:

- <VASTAdData>indica que uma resposta de ad VASTAS está incorporada dentro do ficheiro VMAP
- <AdTagURI>um URI que referencia uma resposta de ad a partir de outro sistema
- <CustomAdData>-um arbitrário esse respresents uma resposta que não sejam VASTA de cadeia

Neste exemplo, uma resposta na linha ad for especificada com um <VASTAdData> elemento que contém uma resposta de ad VASTA. Para mais informações sobre os outros elementos, consulte o artigo [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

O elemento <**AdBreak**> Também pode conter um elemento de <**TrackingEvents**>. O elemento <**TrackingEvents**> permite-lhe controlar o início ou fim de uma quebra de ad ou se Ocorreu um erro durante a quebra de ad. O elemento <**TrackingEvents**> contém um ou mais <**de controlo**> elementos, cada um dos quais Especifica um evento de controlo e um controlo URI. Os eventos de controlo possíveis são:

1. breakStart – controla o início de uma quebra de ad
1. breakEnd – controlar a conclusão de uma quebra de ad
1. Erro de – controla um erro que ocorreram durante a quebra de ad

O exemplo seguinte mostra um ficheiro VMAP que especifica os eventos de controlo

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Para mais informações sobre o elemento <**TrackingEvents**> e sites secundários, consulte o artigo http://iab.org/VMAP.pdf

###<a name="using-a-media-abstract-sequencing-template-mast-file"></a>Utilizar um resumo de multimédia Sequencing ficheiro de modelo (LATA)

Um ficheiro de LATA permite-lhe especificar accionadores que definem quando é apresentado um anúncio. Segue-se um ficheiro de LATA de exemplo contém accionadores para ad de recuperar um tipo de ficheiro, ad uma agregação meados e um ad pós recuperar.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>

 

Um ficheiro de LATA começa com um **<MAST>** elemento que contém um **<triggers>** elemento. O <triggers> elemento contém um ou mais **<trigger>** elementos que definem quando um ad deve ser reproduzido. 

O **<trigger>** elemento contém um **<startConditions>** elemento que especificar quando um anúncio deverá começar a ser reproduzido. O **<startConditions>** elemento contém um ou mais <condition> elementos. Quando cada <condition> for avaliada como verdadeira um accionador é iniciado, ou revogado dependendo se o <condition> encontra-se uma **< startConditions**> ou **<endConditions>** elemento respetivamente. Quando vários <condition> elementos estão presentes, serem tratadas como um OR implícito, qualquer condição avaliar como verdadeiro irão provocar o accionador iniciar. <condition>elementos podem ser aninhados. Quando subordinado <condition> elementos são predefinidos, serem tratadas como um AND implícito, todas as condições têm de ser avaliados como true para que o accionador iniciar. O <condition> elemento contém os seguintes atributos que definem a condição: 

1. **tipo de** – Especifica o tipo de condição, evento ou propriedade
1. **nome** – o nome da propriedade ou evento para ser utilizado durante a avaliação
1. **valor** – o valor que será avaliada uma propriedade contra
1. **operador** – a operação a utilizar durante a avaliação: EQ (igual), NEQ (não igual), GTR (maior), GEQ (maior ou igual), LT (inferior), LEQ (menor ou igual), MOD (módulo)

**<endConditions>**também contêm <condition> elementos. Quando uma condição devolver o valor verdadeiro o accionador é reposta. O <trigger> elemento também contém um <sources> elemento que contém um ou mais <source> elementos. O <source> elementos definem o URI para a resposta ad e o tipo de resposta do ad. Neste exemplo, um URI é dado a uma VASTA resposta. 


    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
 

###<a name="using-video-player-ad-interface-definition-vpaid"></a>Utilizar a definição de Interface de vídeo Ad Player (VPAID)

VPAID é uma API para ativar a unidades de ad executável comunicar com um leitor de vídeo. Esta opção permite-experiências ad extremamente interativos. O utilizador pode interagir com o ad e o ad pode responder a acções tomadas pelo Visualizador. Por exemplo, um ad poderá apresentar botões que permitem ao utilizador ver mais informações ou uma versão mais longa do ad. O leitor de vídeo tem de suportar a API VPAID e o ad executável tem de implementar a API. Quando um leitor de pedidos de que um anúncio de um servidor do ad o servidor poderá responder com uma VASTA resposta que contém uma ad VPAID.

Um ad executável é criado no código que tem de ser executado num ambiente runtime como Adobe Flash™ ou JavaScript que pode ser executada num browser. Quando um servidor do ad devolve uma VASTA resposta que contém uma ad VPAID, o valor da apiFramework do atributo da <MediaFile> elemento tem de ser "VPAID". Este atributo Especifica que o ad contido é um anúncio executável VPAID. O atributo type tem de ser definido com o tipo MIME do executável, tal como "aplicação/x-shockwave-flash" ou "x/aplicação-javascript". A seguinte mostra o fragmento XML a <MediaFile> elemento a partir de uma VASTA resposta que contém um anúncio executável VPAID. 

    
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
 

Um ad executável pode ser inicializado utilizando o <AdParameters> elemento o <Linear> ou <NonLinear> elementos na resposta a uma VASTA. Para mais informações sobre o <AdParameters> elemento, consulte o artigo [VASTA 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). Para mais informações sobre a API do VPAID, consulte o artigo [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

##<a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Aplicação do Windows ou o Windows Phone 8 Player com suporte de Ad

A plataforma Microsoft Media: Player Framework para o Windows 8 e Windows Phone 8 contém um conjunto de aplicações de exemplo que lhe mostram como implementar uma aplicação do leitor de vídeo utilizando a arquitetura do. Pode transferir a arquitetura do leitor e com os exemplos do [leitor de quadro para o Windows 8 e Windows Phone 8](https://playerframework.codeplex.com).

Quando abre a solução Microsoft.PlayerFramework.Xaml.Samples irá ver um número de pastas do projeto. A pasta de publicidade contém o código de exemplo relevante para a criação de um leitor de vídeo com o suporte de ad. Dentro de publicidade pasta é um número de ficheiros XAML/cs cada dos quais Mostrar como inserir anúncios de forma diferente. A lista seguinte descreve cada um:

- AdPodPage.xaml mostra como apresentar uma vagem ad.
- AdSchedulingPage.xaml mostra como agendar anúncios.
- FreeWheelPage.xaml mostra como utilizar o plug-in FreeWheel para agendar anúncios.
- MastPage.xaml mostra como agendar anúncios com um ficheiro de LATA.
- ProgrammaticAdPage.xaml mostra como agendar através de programação anúncios num vídeo.
- ScheduleClipPage.xaml mostra como agendar uma ad sem um ficheiro grande.
- VastLinearCompanionPage.xaml mostra como inserir um linear e ad acompanha.
- VastNonLinearPage.xaml mostra como inserir um ad não linear.
- VmapPage.xaml mostra como especificar anúncios com um ficheiro VMAP.

Cada uma das seguintes amostras utiliza a classe de MediaPlayer definida pela estrutura do leitor. A maior parte dos exemplos utilizam Plug-ins do que adicionam suporte para vários formatos de resposta do ad. O exemplo ProgrammaticAdPage através de programação interage com uma instância de MediaPlayer.

###<a name="adpodpage-sample"></a>Exemplo de AdPodPage

Este exemplo utiliza a AdSchedulerPlugin para definir quando apresentar um anúncio. Neste exemplo, um anúncio rollup meados está agendado para ser reproduzido após 5 segundos. Vagem ad (um grupo de anúncios para apresentar por ordem) está especificada num ficheiro VASTA devolvido de um servidor do ad. URI para o ficheiro vasto especificado na <RemoteAdSource> elemento.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
    
        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>
    
                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>
    
                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Para mais informações sobre o AdSchedulerPlugin, consulte o artigo [publicidade no quadro leitor no Windows 8 e Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

###<a name="adschedulingpage"></a>AdSchedulingPage

Este exemplo utiliza também a AdSchedulerPlugin. O agenda três anúncios, um ad pré- imagens da, ad uma agregação meados e um ad pós recuperar. O URI a VAST para cada ad especificado num <RemoteAdSource> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>
    
                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


###<a name="freewheelpage"></a>FreeWheelPage

Este exemplo utiliza a FreeWheelPlugin que especifica um atributo de origem que especifica um URI que aponta para um ficheiro de SmartXML que especifica ad conteúdo, bem como ad informações de agendamento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="mastpage"></a>MastPage

Este exemplo utiliza a MastSchedulerPlugin que permite-lhe utilizar um ficheiro de LATA. O atributo de origem Especifica a localização do ficheiro LATA.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="programmaticadpage"></a>ProgrammaticAdPage

Este exemplo através de programação interage com o MediaPlayer. O ficheiro ProgrammaticAdPage.xaml iniciar instância a MediaPlayer:

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

O ficheiro ProgrammaticAdPage.xaml.cs cria uma AdHandlerPlugin, adiciona uma TimelineMarker para especificar quando um ad deve ser apresentado e, em seguida, adiciona um processador para o evento de MarkerReached que carrega uma RemoteAdSource especificando um URI para um ficheiro vasto e, em seguida, pode ser reproduzido o ad.
    
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;
    
            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }
    
            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

###<a name="scheduleclippage"></a>ScheduleClipPage

Este exemplo utiliza a AdSchedulerPlugin para agendar uma ad rollup meados ao especificar um ficheiro. wmv que contém o ad.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearcompanionpage"></a>VastLinearCompanionPage

Este exemplo ilustra como utilizar o AdSchedulerPlugin para agendar uma ad linear de agregação meados com um ad acompanha. O <RemoteAdSource> elemento Especifica a localização do ficheiro VASTA.
    
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage

Este exemplo utiliza a AdSchedulerPlugin para agendar uma linear e um ad não linear. A localização do ficheiro VASTAS for especificada com o <RemoteAdSource> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
                            
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vmappage"></a>VMAPPage

Este amostras utiliza o VmapSchedulerPlugin para agendar anúncios com um ficheiro VMAP. URI para o ficheiro VMAP for especificado no atributo origem da <VmapSchedulerPlugin> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

##<a name="implementing-an-ios-video-player-with-ad-support"></a>Implementar um leitor de vídeo com o suporte de Ad iOS


A plataforma Microsoft Media: Framework Player para iOS contém um conjunto de aplicações de exemplo que lhe mostram como implementar uma aplicação do leitor de vídeo utilizando a arquitetura do. Pode transferir a arquitetura do leitor e com os exemplos do [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). A página de github tem uma ligação para um Wiki que contém informações adicionais sobre a arquitetura do leitor e uma introdução à amostra de leitor: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).


###<a name="scheduling-ads-with-vmap"></a>Agendamento anúncios com VMAP

O exemplo seguinte mostra como agendar anúncios com um ficheiro VMAP.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest
    
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

###<a name="scheduling-ads-with-vast"></a>Agendamento anúncios com VAST

O exemplo seguinte mostra como agendar uma ad VASTA de encadernação atrasada.
    
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
         
   O exemplo seguinte mostra como agendar uma antecipada encadernação VASTA pelo ad.
Exemplo: 4 agenda uma antecipada encadernação ad VASTA //Download a VAST ficheiro, se (! [ framework.adResolver downloadManifest: & manifesto withURL: [NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) {[logFrameworkError autonomamente];} else {adLinearTime.startTime = 7; adLinearTime.duration = 0;
        
        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

O exemplo seguinte mostra como inserir um anúncio utilizando aproximada cortar edição (RCE)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar uma vagem ad.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;
    
    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar uma ad meados rollup não autocolantes. Um ad não autocolantes é reproduzido apenas uma vez, independentemente de quaisquer à procura o Visualizador executa a.
    
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar uma ad rollup meados autocolante. Um autocolante ad será apresentada sempre que o ponto de especificado na linha de tempo vídeo é atingido.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


O exemplo seguinte mostra como agendar uma ad pós recuperar.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar uma ad pré-lançamento.
    
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo seguinte mostra como agendar uma ad rollup meados sobreposição.
    
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
##<a name="see-also"></a>Consulte também

[Desenvolver aplicações de leitor de vídeo](media-services-develop-video-players.md)
