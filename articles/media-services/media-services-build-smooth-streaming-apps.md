<properties 
    pageTitle="Lisa transmissão Tutorial de aplicação da loja Windows | Microsoft Azure" 
    description="Saiba como utilizar dos serviços de multimédia do Azure para criar uma aplicação da loja Windows c# com um controlo de XML MediaElement à sequência suaves reproduzir conteúdo." 
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



#<a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Como criar um bom transmissão aplicação da loja Windows

O suaves transmissão cliente SDK para o Windows 8 permite que os programadores a criação de aplicações da loja Windows que podem reproduzir conteúdo a pedido e direto transmissão suaves. Para além da reprodução básica de transmissão suaves o conteúdo, que o SDK também fornece as funcionalidades de avançada, como Microsoft PlayReady proteção, qualidade de áudio de restrição de nível, Live DVR, transmitir em fluxo mudar, a aguardar as atualizações de estado (como alterações de nível de qualidade) e eventos de erro etc. Para obter mais informações das funcionalidades suportadas, consulte as [notas de lançamento](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Para mais informações, consulte o artigo [Player Framework para o Windows 8](http://playerframework.codeplex.com/). 

Neste tutorial contém quatro lições:

1. Criar uma aplicação de arquivo de transmissão de suaves básicas
2. Adicionar uma barra de controlo de deslize para controlar o progresso de multimédia
3. Selecione sequências de transmissão suaves
4. Selecione faixas de transmissão suaves

##<a name="prerequisites"></a>Pré-requisitos

- Windows 8, versão de 32 bits ou 64 bits. Pode obter o [Windows 8 Enterprise avaliação](http://msdn.microsoft.com/evalcenter/jj554510.aspx) do MSDN.
- Visual Studio 2012http ou Visual Studio Express 2012http (ou uma versão posterior). Pode obter a versão de avaliação a partir de [aqui](http://www.microsoft.com/visualstudio/11/downloads).
- [Microsoft suave transmissão cliente SDK para o Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).


A solução completa para cada lição pode ser transferida das exemplos de código de programador do MSDN (código Gallery): 

- [Lição 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - um bom simples no Windows 8 transmissão Media Player, 
- Controlar a [Lição 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - um leitor de multimédia simples no Windows 8 transmissão suaves com uma barra de controlo de deslize, 
- [Lição 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - um bom no Windows 8 transmissão Media Player com seleção da cadeia,  
- [Lição 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - um bom no Windows 8 transmissão Media Player com registar seleção.

##<a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lição 1: Criar uma aplicação de arquivo de transmissão de suaves básicas

Esta lição, irá criar uma aplicação da loja Windows com um controlo de MediaElement para reproduzir sequência suave conteúdo.  A aplicação em execução tem a seguinte apresentação:

![Exemplo de aplicação da loja Windows transmissão suave][PlayerApplication]
 
Para mais informações sobre como desenvolver aplicações da loja Windows, consulte o artigo [desenvolver excelente aplicações para o Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). Esta lição contém os seguintes procedimentos:

1.  Criar um projeto da loja Windows
2.  Estruturar a interface de utilizador (XAML)
3.  Modificar o código por trás do ficheiro
4.  Compilar e testar a aplicação

**Para criar um projeto da loja Windows**

1.  Execute o Visual Studio 2012 ou posterior.
2.  No menu **ficheiro** , clique em **Novo**e, em seguida, clique em **projeto**.
3.  Caixa de diálogo novo projeto, escreva ou selecione os seguintes valores:

Nome|Valor
---|---
Grupo de modelo|Instalado/modelos/Visual c# / armazenar do Windows
Modelo|Aplicação vazia (XAML)
Nome|SSPlayer
Localização|C:\SSTutorials
Nome da solução|SSPlayer
Criar directório para solução|(selecionada)

4.  Clique em **OK**.

**Para adicionar uma referência para o cliente SDK suaves transmissão**

1.  Solução Explorer, com o botão direito **SSPlayer**e, em seguida, clique em **Adicionar referência**.
2.  Escreva ou selecione os seguintes valores:

Nome|Valor
---|---
Grupo de referência|Extensões do Windows
Referência|Selecione Microsoft suave transmissão SDK do cliente para o Windows 8 e Microsoft Visual C++ Runtime pacote
    
3.  Clique em **OK**. 

Depois de adicionar as referências, tem de selecionar a plataforma alvo (x64 ou x86), adicionar referências não irão funcionar para a configuração de plataforma Any CPU.  No Explorador de solução, irá ver amarelo de aviso para estas adicionado referências.

**Para a interface de utilizador do leitor de estrutura**

1.  Solução Explorer, faça duplo clique em **MainPage.xaml** para o abrir na vista de estrutura.
2.  Localize o ** &lt;grelha&gt; ** e ** &lt;/Grid&gt; ** etiquetas o ficheiro XAML e cole o seguinte código entre as duas etiquetas:

        <Grid.RowDefinitions>
            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
        </Grid.RowDefinitions>
        
        <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
        </StackPanel>

        <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
        </StackPanel>

        <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
        </StackPanel>

    O controlo de MediaElement é utilizada para multimédia de reprodução. O controlo de deslize com o nome sliderProgress será utilizado para controlar o progresso de multimédia na próxima lição.

3.  Prima **CTRL + G** para guardar o ficheiro.

O controlo de MediaElement não suporta a transmissão suaves conteúdo out-de-box. Para ativar o suporte técnico da transmissão suaves, tem de registar o processador de sequência de bytes suaves transmissão por extensão de nome de ficheiro e o tipo MIME.  Para registar, utilize o método de MediaExtensionManager.RegisterByteStremHandler do espaço de nomes Windows.Media.

Neste ficheiro XAML, algum processadores de evento estão associados a controlos.  Tem de definir esses processadores de eventos.

**Para modificar o código de ficheiro**

1.  Solução Explorer, com o botão direito **MainPage.xaml**e, em seguida, clique em **Ver código**.
2.  Na parte superior do ficheiro, adicione o seguinte utilizando instrução:

        using Windows.Media;

3.  No início da classe **MainPage** , adicione o membro de dados seguinte:

        private MediaExtensionManager extensions = new MediaExtensionManager();

4.  No final do construtor **MainPage** , adicione as duas linhas seguintes:

        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
        
5.  No final da categoria **MainPage** , cole o seguinte código:

        #region UI Button Click Events
        private void btnPlay_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Play();
            txtStatus.Text = "MediaElement is playing ...";
        }
        
        private void btnPause_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Pause();
            txtStatus.Text = "MediaElement is paused";
        }
        
        private void btnSetSource_Click(object sender, RoutedEventArgs e)
        {
            sliderProgress.Value = 0;
            mediaElement.Source = new Uri(txtMediaSource.Text);
        
            if (chkAutoPlay.IsChecked == true)
            {
                txtStatus.Text = "MediaElement is playing ...";
            }
            else
            {
                txtStatus.Text = "Click the Play button to play the media source.";
            }
        }
        
        private void btnStop_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Stop();
            txtStatus.Text = "MediaElement is stopped";
        }
        
        private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            txtStatus.Text = "Seek to position " + sliderProgress.Value;
            mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
        }
        #endregion

    Processador de eventos de sliderProgress_PointerPressed é definido aqui.  Existem mais funciona para fazer para tê-lo a trabalhar, que irá disponíveis na próxima lição deste tutorial.
6.  Prima **CTRL + G** para guardar o ficheiro.

A terminar que o código de ficheiro deve ter este aspeto:

![Codeview numa aplicação do Visual Studio de suaves transmissão da loja Windows][CodeViewPic]

**Compilar e testar a aplicação**

1.  No menu de **Criar** , clique em **Gestor de configuração**.
2.  Alterar a **plataforma de solução activa** para que correspondam às sua plataforma de desenvolvimento.
3.  Prima **F6** para compilar o projeto. 
4.  Prima **F5** para executar a aplicação.
5.  Na parte superior da aplicação, pode utilizar o URL de transmissão suaves predefinido ou introduza um diferente. 
6.  Clique em **definir a origem**. Uma vez que **Reproduzir automática** está ativada por predefinição, os elementos multimédia serão reproduzido automaticamente.  Pode controlar os elementos multimédia utilizando o **Reproduzir**, **Pausa** e **Parar** botões.  Pode controlar o volume de multimédia utilizando a barra de deslocamento vertical.  No entanto o controlo de deslize horizontal para controlar o progresso de multimédia ainda não está totalmente implementado. 

Concluiu lesson1.  Esta lição, utiliza o controlo de MediaElement para reproduzir conteúdo transmissão suaves.  Na próxima lição, irá adicionar um controlo de deslize para controlar o progresso do conteúdo transmissão suaves.


##<a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lição 2: Adicionar uma barra de controlo de deslize para controlar o progresso de multimédia
Lição 1, criou uma aplicação da loja Windows com um controlo de MediaElement XAML para reproduzir conteúdo de multimédia transmissão suaves.  Chegar algumas funções de multimédia básicas, como iniciar, parar e colocar em pausa.  Esta lição, irá adicionar um controlo de barra de controlo de deslize para a aplicação.

Neste tutorial, irá Utilizamos um temporizador para atualizar a posição do controlo de deslize com base em da posição atual do controlo MediaElement.  O controlo de deslize início e fim tempo também precisa de ser atualizadas em caso de conteúdo em directo.  Isto pode ser melhor resolvido o evento de actualização de origem ajustável.

Origens de multimédia são objetos que geram dados de multimédia.  A resolução de origem assuma uma sequência de URL ou de byte e cria a origem de multimédia adequado para esse conteúdo.  A resolução de origem é a forma padrão para as aplicações criar origens de multimédia. 

Esta lição contém os seguintes procedimentos:

1.  Registe-se a alça de transmissão suaves 
2.  Adicionar os processadores de eventos ao nível do Gestor de origem ajustável
3.  Adicionar os processadores de nível de evento ajustável origem
4.  Adicionar MediaElement processadores de eventos
5.  Adicionar o controlo de deslize relacionado código de barras
6.  Compilar e testar a aplicação

**Para registar o processador de sequência de bytes transmissão suaves e transmitir a propertyset**

1.  Solução Explorer, clique com o botão direito do rato em **MainPage.xaml**e, em seguida, clique em **Ver código**.
2.  No início do ficheiro, adicione o seguinte utilizando instrução:

        using Microsoft.Media.AdaptiveStreaming;

3.  No início da classe MainPage, adicione membros de dados que se seguem:

        private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
        private IAdaptiveSourceManager adaptiveSourceManager;
    
4.  Dentro do construtor de **MainPage** , adicione o código seguinte após a **Isto. Iniciar Components();** linha e linhas de código de registo escritos na lição anterior:
    
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
    
5.  Dentro do construtor de **MainPage** , modificar os dois métodos de RegisterByteStreamHandler para adicionar o quarto parâmetros:

        // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
        // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
        // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
        propertySet);

6.  Prima **CTRL + G** para guardar o ficheiro.

**Para adicionar o processador de eventos ao nível do Gestor de origem ajustável**

1.  Solução Explorer, clique com o botão direito do rato em **MainPage.xaml**e, em seguida, clique em **Ver código**.
2.  Dentro de classe **MainPage** , adicione o membro de dados seguinte:

        private AdaptiveSource adaptiveSource = null;

3.  No final da classe **MainPage** , adicione o seguinte processador de eventos:
    
        #region Adaptive Source Manager Level Events
        private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
        }
        #endregion Adaptive Source Manager Level Events

4.  No final do construtor **MainPage** , adicione a linha seguinte para subscrever o evento Abrir origem ajustável:
    
    + adaptiveSourceManager.AdaptiveSourceOpenedEvent = AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened) novo;

5.  Prima **CTRL + G** para guardar o ficheiro.

**Para adicionar origem ajustável processadores de nível de evento**

1.  Solução Explorer, clique com o botão direito do rato em **MainPage.xaml**e, em seguida, clique em **Ver código**.
2.  Dentro de classe **MainPage** , adicione o membro de dados seguinte:
    
        private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
        private Manifest manifestObject;
    
3.  No final da classe **MainPage** , adicione os seguintes processadores de evento:

        #region Adaptive Source Level Events
        private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
        }
        
        private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
        {
            adaptiveSourceStatusUpdate = args;
        }
        
        private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
        {
            txtStatus.Text = "Error: " + args.HttpResponse;
        }
        #endregion Adaptive Source Level Events

4.  No final do método **mediaElement AdaptiveSourceOpened** , adicione o seguinte código para subscrever os eventos:
    
        adaptiveSource.ManifestReadyEvent +=
                    mediaElement_ManifestReady;
        adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
            mediaElement_AdaptiveSourceStatusUpdated;
        adaptiveSource.AdaptiveSourceFailedEvent += 
            mediaElement_AdaptiveSourceFailed;
    
5.  Prima **CTRL + G** para guardar o ficheiro.

Os eventos mesmo estão disponíveis no ajustável Gestor nível de origem, assim, que pode ser utilizado para o processamento de funcionalidade comuns a todos os elementos de multimédia na aplicação. Cada AdaptiveSource inclui os suas próprias eventos e todos os eventos AdaptiveSource serão propagados em AdaptiveSourceManager.

**Para adicionar processadores de eventos do elemento de multimédia**

1.  Solução Explorer, clique com o botão direito do rato em **MainPage.xaml**e, em seguida, clique em **Ver código**.
2.  No final da classe **MainPage** , adicione os seguintes processadores de evento:
    
        #region Media Element Event Handlers 
        private void MediaOpened(object sender, RoutedEventArgs e)
        {
            txtStatus.Text = "MediaElement opened";
        }
        
        private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
        {
            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
        }
        
        private void MediaEnded(object sender, RoutedEventArgs e)
        {
            txtStatus.Text ="MediaElement ended.";
        }
        #endregion Media Element Event Handlers

3.  No final do construtor **MainPage** , adicione o seguinte código para inferior à linha para os eventos:
    
        mediaElement.MediaOpened += MediaOpened;
        mediaElement.MediaEnded += MediaEnded;
        mediaElement.MediaFailed += MediaFailed;

4.  Prima **CTRL + G** para guardar o ficheiro.

**Para adicionar uma barra de controlo de deslize relacionados com código**

1.  Solução Explorer, clique com o botão direito do rato em **MainPage.xaml**e, em seguida, clique em **Ver código**.
2.  No início do ficheiro, adicione o seguinte utilizando instrução:
    
        using Windows.UI.Core;

3.  Dentro de classe **MainPage** , adicione membros de dados que se seguem:
    
        public static CoreDispatcher _dispatcher;
        private DispatcherTimer sliderPositionUpdateDispatcher;
    
4.  No final do construtor **MainPage** , adicione o seguinte código:

        _dispatcher = Window.Current.Dispatcher;
        PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
        sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
    
5.  No final da classe **MainPage** , adicione o seguinte código:
    
        #region sliderMediaPlayer
        private double SliderFrequency(TimeSpan timevalue)
        {
            long absvalue = 0;
            double stepfrequency = -1;
        
            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }
        
            TimeSpan totalDVRDuration = new TimeSpan(absvalue);
        
            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }
        
            return stepfrequency;
        }
        
        void updateSliderPositionoNTicks(object sender, object e)
        {
            sliderProgress.Value = mediaElement.Position.TotalSeconds;
        }
        
        public void setupTimer()
        {
            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
        }

        public void startTimer()
        {
            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderStartTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderEndTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
        }
        #endregion sliderMediaPlayer

    **Nota:** CoreDispatcher é utilizada para efetuar alterações às tópico de IU de que não sejam de IU de tópico. Em caso de congestionamento no tópico distribuidor, programador pode optar por utilizar distribuidor fornecida pela elemento da IU por si tenciona atualizar.  Por exemplo:
    
        await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
          timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
        double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
          sliderProgress.Maximum = absvalue; }); 
        

6.  No final do método **mediaElement_AdaptiveSourceStatusUpdated** , adicione o seguinte código:
    
        setSliderStartTime(args.StartTime);
        setSliderEndTime(args.EndTime);

7.  No final do método **MediaOpened** , adicione o seguinte código:
    
    sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan); sliderProgress.Width = mediaElement.Width; setupTimer();

8.  Prima **CTRL + G** para guardar o ficheiro.

**Compilar e testar a aplicação**

1. Prima **F6** para compilar o projeto. 
2.  Prima **F5** para executar a aplicação.
3.  Na parte superior da aplicação, pode utilizar o URL de transmissão suaves predefinido ou introduza um diferente. 
4.  Clique em **definir a origem**. 
5.  Teste a barra de deslocamento.

Concluiu lição 2.  Esta lição adicionou um controlo de deslize para a aplicação. 

##<a name="lesson-3-select-smooth-streaming-streams"></a>Lição 3: Selecione sequências de transmissão suaves
Transmissão suave é capaz para transmitir conteúdos com vários faixas de áudio idioma que estão selecionável pelos visualizadores.  Esta lição permitirá visualizadores selecionar sequências. Esta lição contém os seguintes procedimentos:

1. Modificar o ficheiro XAML
2. Modificar o ficheiro de behand de código
3. Compilar e testar a aplicação


**Para modificar o ficheiro XAML**

1. Solução Explorer, com o botão direito **MainPage.xaml**e, em seguida, clique em **Vista de Designer**.
2. Localize &lt;Grid.RowDefinitions&gt;e modificar o RowDefinitions, de modo que tem a seguinte apresentação:

        <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
        </Grid.RowDefinitions>

3. Dentro de &lt;grelha&gt;&lt;/Grid&gt; etiquetas, adicione o seguinte código para definir um controlo de caixa de listagem, para que os utilizadores podem ver a lista de fluxos de disponíveis e selecione sequências de:

        <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
        </Grid>

4. Prima **CTRL + G** para guardar as alterações.


**Para modificar o código de ficheiro**

1. Solução Explorer, com o botão direito **MainPage.xaml**e, em seguida, clique em **Ver código**.
2. Dentro do espaço de nomes SSPlayer, adicionar uma nova classe: #region classe da cadeia
    
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
    
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
    
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
    
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream

3. No início da classe MainPage, adicione as seguintes definições de variáveis:

        private List<Stream> availableStreams;
        private List<Stream> availableAudioStreams;
        private List<Stream> availableTextStreams;
        private List<Stream> availableVideoStreams;

4. Dentro de classe MainPage, adicione a região seguinte:

        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
        
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
        
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
        
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
        
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
        
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
        
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
        
            // Select the frist video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
        
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
        
            // Select the frist audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
        
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
        
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection

5. Localize o método de mediaElement_ManifestReady, acrescentar o código seguinte no final da função:
    
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();

    Por isso, quando MediaElement manifesto estiver pronto, o código obtém uma lista das sequências disponíveis e preenche a caixa de listagem de IU com a lista.

6. Dentro de classe MainPage, localize a IU botões clique eventos região e, em seguida, adicione a definição de função seguinte:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Compilar e testar a aplicação**

1. Prima **F6** para compilar o projeto. 
2.  Prima **F5** para executar a aplicação.
3.  Na parte superior da aplicação, pode utilizar o URL de transmissão suaves predefinido ou introduza um diferente. 
4.  Clique em **definir a origem**. 
5.  O idioma predefinido é audio_eng. Experimente alternar entre audio_eng e audio_es. Sempre, seleccionar uma nova sequência, tem de clicar no botão Submeter.

Concluiu lição 3.  Esta lição, adicione a funcionalidade para escolher sequências.

##<a name="lesson-4-select-smooth-streaming-tracks"></a>Lição 4: Selecione faixas de transmissão suaves
Uma apresentação de transmissão suaves pode conter vários ficheiros de vídeo codificados com níveis de qualidade diferente (bit velocidades) e resoluções. Esta lição, irá permitem aos utilizadores selecione faixas. Esta lição contém os seguintes procedimentos:

1. Modificar o ficheiro XAML
2. Modificar o ficheiro de behand de código
3. Compilar e testar a aplicação

**Para modificar o ficheiro XAML**

1. Solução Explorer, com o botão direito **MainPage.xaml**e, em seguida, clique em **Vista de Designer**.
2. Localize o &lt;grelha&gt; marcar com o nome **gridStreamAndBitrateSelection**, acrescentar o código seguinte no final da etiqueta:

        <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
        </StackPanel>

3. Prima **CTRL + G** para guardar as alterações de he


**Para modificar o código de ficheiro**

1. Solução Explorer, com o botão direito **MainPage.xaml**e, em seguida, clique em **Ver código**.
2. Dentro do espaço de nomes SSPlayer, adicione uma nova classe:
    
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
    
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
    
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
    
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track

3. No início da classe MainPage, adicione as seguintes definições de variáveis:
    
        private List<Track> availableTracks;

4. Dentro de classe MainPage, adicione a região seguinte:
    
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>

        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();

            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;

                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }

        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }

        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }

        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }

        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection

5. Localize o método de mediaElement_ManifestReady, acrescentar o código seguinte no final da função:

        getTracks(manifestObject);
        refreshAvailableTracksListBoxItemSource();

6. Dentro de classe MainPage, localize a IU botões clique eventos região e, em seguida, adicione a definição de função seguinte:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Compilar e testar a aplicação**

1. Prima **F6** para compilar o projeto. 
2.  Prima **F5** para executar a aplicação.
3.  Na parte superior da aplicação, pode utilizar o URL de transmissão suaves predefinido ou introduza um diferente. 
4.  Clique em **definir a origem**. 
5.  Por predefinição, todas as pistas da sequência de vídeo são selecionadas. Para experimentar as alterações de taxa de bit, pode selecionar a velocidade de bits mais baixa disponível e, em seguida, selecione a velocidade de bits mais alta disponível. Tem de clicar em submeter após cada alteração.  Pode ver as alterações de qualidade do vídeo.

Concluiu Lição 4.  Esta lição, adicione a funcionalidade para escolher faixas.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="other-resources"></a>Outros recursos:
- [Como criar uma aplicação suaves JavaScript de 8 Windows transmissão com funcionalidades avançadas](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
- [Suave descrição geral de técnica transmissão](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
 
