<properties 
    pageTitle="Suave transmissão Plug-in para a arquitetura de multimédia abrir origem" 
    description="Saiba como utilizar o plug-in Azure serviços suaves sequência de multimédia para o Adobe abrir origem multimédia Framework." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Como utilizar o bom Microsoft transmissão Plug-in para a arquitetura de multimédia do Adobe abrir origem

##<a name="overview"></a>Descrição geral


O plug-in Microsoft suaves Streaming para abrir origem multimédia Framework 2.0 (SS para OSMF) expande as capacidades de predefinido do OSMF e adiciona Microsoft suaves Streaming reprodução do conteúdo para os leitores OSMF novos e existentes. O plug-in também adiciona transmissão suaves capacidades de reprodução para reprodução de multimédia Strobe (Multiprocessing).

SS para OSMF inclui duas versões do plug-in:

- Estático transmissão suaves Plug-in para OSMF (.swc)

- Dinâmico transmissão suaves Plug-in para OSMF (. swf)

Este documento assume que o leitor tem um conhecimentos gerais sobre o funcionamento de OSMF e OSMF plug-ins. Para mais informações sobre OSMF, consulte a documentação no [site OSMF oficial](http://osmf.org/).

###<a name="smooth-streaming-plugin-for-osmf-20"></a>Suave transmissão Plug-in para OSMF 2.0

O plug-in suporta carregamento e reprodução de conteúdo de transmissão suaves a pedido com as seguintes funcionalidades:

- Reprodução de transmissão suaves a pedido (reproduzir, pausa, atingir, parar)
- Reprodução de transmissão suaves direto (Play)
- Funções DVR direto (pausa, atingir, DVR reprodução, Go to Live)
- Suporte para codecs de vídeo - h. 264
- Suporte para codecs de áudio - AAC
- Vários idiomas áudio mudar com APIs incorporados OSMF
- Seleção de qualidade de reprodução máximo com APIs incorporados OSMF
- Carro fechado legendas com legendas OSMF Plug-in
- Adobe&reg; Flash&reg; Player 11,4 ou superior.
- Esta versão suporta apenas OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Funcionalidades suportadas e problemas conhecidos

Para uma lista completa das funcionalidades suportadas, as funcionalidades não suportadas e problemas conhecidos, consulte [Este documento](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).


## <a name="loading-the-plugin"></a>Carregar o plug-in
Plug-ins do OSMF podem ser carregados estática (em tempo de compilação) ou dinamicamente (em tempo de execução). O plug-in transmissão suaves para transferência OSMF inclui dinâmicas e estáticas versões.

- Carregamento estático: para carregar estática, é necessário um ficheiro de biblioteca estático (SWC). Plug-ins estáticos são adicionados como uma referência a projetos e em série no interior o ficheiro de exportação final ao tempo de compilação.

- Carregamento dinâmico: para carregar dinamicamente, é necessário um ficheiro de (SWF) compilado. Plug-ins dinâmicos são carregados no runtime do e não incluídos no resultado do projeto. (Saída compilada) Plug-ins dinâmicas podem ser carregados utilizando os protocolos HTTP e o ficheiro.

Para mais informações sobre o carregamento estático e dinâmico, consulte a [página de plug-in OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)oficial.

###<a name="ss-for-osmf-static-loading"></a>SS para OSMF estático carregamento
O fragmento de código abaixo mostra como carregar o plug-in SS para OSMF estática e reproduzir um vídeo básico utilizando OSMF MediaFactory classe. Antes de a incluir SS código OSMF de, certifique-se de que a referência de projeto inclui o plug-in de estático de "MSAdaptiveStreamingPlugin-v1.0.3 osmf2.0.swc".

```
package 
{
    
    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    
    
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        

        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }
    
        private function initMediaPlayer():void
        {
        
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            
            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}
```


###<a name="ss-for-osmf-dynamic-loading"></a>SS para carregamento dinâmico OSMF

O fragmento de código abaixo mostra como carregar o plug-in SS para OSMF dinamicamente e reproduzir uma basic vídeo utilizando a classe de OSMF MediaFactory. Antes de incluindo SS código OSMF de, copie o plug-in dinâmico do "MSAdaptiveStreamingPlugin-v1.0.3 osmf2.0.swf" para a pasta de projeto se pretende carregar utilizando o protocolo de ficheiros ou copiar num servidor web para a carga HTTP. Não é necessário para incluir "MSAdaptiveStreamingPlugin-v1.0.3 osmf2.0.swc" nas referências de projeto.

 
{do pacote
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;

    
    //Sets the size of the SWF
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        
        
        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }
        
        private function initMediaPlayer():void
        {
            
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}

##<a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Reprodução de multimédia strobe com o plug-in dinâmicos do SS ODMF

Transmissão suaves para OSMF dinâmico Plug-in é compatível com a [Reprodução de multimédia Strobe (Multiprocessing)](http://osmf.org/strobe_mediaplayback.html). Pode utilizar SS para plug-in OSMF para adicionar a transmissão suaves reprodução do conteúdo ao SMP. Para fazer isto, copie "MSAdaptiveStreamingPlugin-v1.0.3 osmf2.0.swf" num servidor web para a carga HTTP através dos seguintes passos:

1.  Procure a [página de configuração de reprodução de multimédia Strobe](http://osmf.org/dev/2.0gm/setup.html). 
2.  Definir o src a uma origem de transmissão suaves, (por exemplo, http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.  Efetue as alterações de configuração pretendida e clique em pré-visualizar e actualizar.
 
    **Nota** O servidor content web necessita de um crossdomain.xml válido. 
4.  Copie e cole o código a uma página HTML simple utilizando o editor de texto Favoritos, tal como no exemplo seguinte:



        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



5. Adicione o plug-in do suaves OSMF transmissão para o código de incorporação e guarde.

        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>


6.  Guardar a sua página HTML e publicar num servidor web. Navegue para a página web publicada utilizando o Flash favorita&reg; Player com capacidade de browser da Internet (Internet Explorer, Chrome, Firefox, assim sucessivamente).
7.  Desfrute de transmissão suaves conteúdo dentro da Adobe&reg; Flash&reg; Player.

Para mais informações sobre o desenvolvimento de OSMF geral, consulte o artigo oficial [página de desenvolvimento OSMF](http://osmf.org/resources.html).

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Consulte também

[Microsoft ajustável transmissão Plug-in para OSMF actualização](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 
