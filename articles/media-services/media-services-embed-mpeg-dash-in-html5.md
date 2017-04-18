<properties 
    pageTitle="Incorporar um TRAÇO MPEG ajustável transmissão de vídeo numa aplicação HTML5 com DASH.js | Microsoft Azure" 
    description="Este tópico demonstra como incorporar um vídeo de transmissão ajustável MPEG-TRAÇO numa aplicação HTML5 com DASH.js." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Incorporar um TRAÇO MPEG ajustável transmissão de vídeo numa aplicação HTML5 com DASH.js

##<a name="overview"></a>Descrição geral

MPEG-TRAÇO é uma norma ISO para ajustável transmissão de conteúdo de vídeo, que disponibiliza benefícios significativos relacionados para aqueles que pretender para fornecer vídeo de alta qualidade, ajustável transmissão de saída. Com MPEG-travessão, a sequência de vídeo será automaticamente largue a uma definição de inferior quando a rede torna-se tráfego. Este procedimento reduz a probabilidade do Visualizador de ver um vídeo "em pausa" enquanto o Media player transfere os seguintes alguns segundos para reproduzir (também conhecidos por memória intermédia). À medida que reduz o tráfego de rede, o leitor de vídeo sucessivamente irá devolver uma sequência de qualidade superior. Esta capacidade para adaptar a largura de banda necessária também resulta numa hora de início rápida para vídeo. Isto significa que os primeiros segundos podem ser reproduzidos no segmento de qualidade de inferior rápido para transferir e, em seguida, passo até uma conteúdo de uma vez suficientes qualidade superior tenha sido memória intermédia.

Dash.js é um abrir origem MPEG-TRAÇO leitor de vídeo escrito JavaScript. O objetivo é proporcionar um leitor de robusto, em diferentes plataformas que pode ser reutilizado livre aplicações que necessitam de reprodução de vídeo. Fornece reprodução MPEG-TRAÇO qualquer browser que suporte hoje a W3C multimédia origem extensões (MSE), que é o Chrome, Microsoft Edge e IE11 (noutros browsers tem indicado, os respetivos intenção para suportar MSE). Para mais informações sobre DASH.js, js Consulte do repositório de dash.js GitHub.


##<a name="creating-a-browser-based-streaming-video-player"></a>Criar um leitor de vídeo transmissão baseada no browser

Para criar uma página web simples que apresenta um leitor de vídeo com o esperado controla como um reproduzir, pausa, recuar, etc., é necessário:

1. Criar uma página HTML
1. Adicionar a etiqueta de vídeo
1. Adicionar o leitor de dash.js
1. Iniciar o leitor
1. Adicionar algum estilo CSS
1. Ver os resultados num browser que implementa MSE

A iniciar o leitor pode ser concluída em apenas alguns linhas de código JavaScript. Utilizar dash.js, na verdade é tão simples como isto incorporar o vídeo MPEG-TRAÇO nas suas aplicações baseado no browser.

##<a name="creating-the-html-page"></a>Criar a página de HTML

O primeiro passo é criar uma página HTML padrão que contém o elemento de **vídeo** , guardar este ficheiro como basicPlayer.html, tal como ilustrado pelo exemplo que se segue:

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

##<a name="adding-the-dashjs-player"></a>Adicionar o leitor de DASH.js

Para adicionar a aplicação de referência dash.js para a aplicação, terá de captar o ficheiro dash.all.js a partir da 1.0 versão do projeto dash.js. Isto deve ser guardado na pasta JavaScript da sua aplicação. Este ficheiro é um ficheiro de conveniência que obtém todos os códigos de dash.js necessárias de em conjunto num único ficheiro. Se tiver um aspeto à volta do repositório de dash.js, irá encontrar os ficheiros individuais, teste o código e muito mais, mas se todos os que pretende fazer é utilizar dash.js, em seguida, o ficheiro de dash.all.js é o que precisa.

Para adicionar o leitor de dash.js para as suas aplicações, adicione uma etiqueta de script à secção de cabeça de basicPlayer.html:

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Em seguida, crie uma função para iniciar o leitor quando a página for carregada. Adicione o seguinte script após a linha na qual carregado dash.all.js:

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

Esta função cria uma DashContext pela primeira vez. Isto é utilizado para configurar a aplicação para um ambiente do runtime específico. A partir de um ponto de vista técnico, define as classes que a arquitetura de introdução de dependência deve utilizar quando construir a aplicação. Na maioria dos casos, vai utilizar Dash.di.DashContext.

Em seguida, criar instâncias da classe principal do dash.js framework, MediaPlayer. Esta classe contém o essencial métodos, tal como necessários reproduzir e colocar em pausa, gere a relação com o elemento de vídeo e também gere a interpretação do ficheiro de apresentação de multimédia descrição (MPD) que descreve o vídeo para ser reproduzido.

A função startup() a classe de MediaPlayer de chama-se para se certificar de que o leitor está pronto para reproduzir o vídeo. Entre outras coisas esta função assegura que todas as classes (conforme definido pelo contexto) tem sido carregadas. Assim que o leitor estiver pronto, pode anexar o elemento vídeo à mesma, utilizando a função attachView(). Permite que MediaPlayer inserir a sequência de vídeo no elemento e também controlar a reprodução conforme necessário.

Passe o URL do ficheiro MPD para o MediaPlayer para que saiba sobre o vídeo que espera-se para reproduzir. A função setupVideo() acabou de criar terá de ser executada quando a página tem totalmente carregada. Fazê-lo ao utilizar o evento onload do elemento do corpo. Alterar o <body> elemento para:

    <body onload="setupVideo()">

Por fim, defina o tamanho do vídeo elemento utilizando CSS. Num ambiente transmissão ajustável, isto é especialmente importante porque o tamanho do vídeo a ser reproduzido podem ser alteradas como reprodução adapta-se para alterar as condições da rede. Nesta demonstração simples força simplesmente o elemento de vídeo para ser 80% da janela do browser disponíveis adicionando o CSS que se segue à secção do cabeçalho da página:
    
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

##<a name="playing-a-video"></a>Reproduzir um vídeo

Para reproduzir um vídeo, aponte o seu browser, o ficheiro de basicPlayback.html e clique em reproduzir no leitor de vídeo apresentado.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também

[Desenvolver aplicações de leitor de vídeo](media-services-develop-video-players.md)

[GitHub dash.js repositório](https://github.com/Dash-Industry-Forum/dash.js) 
