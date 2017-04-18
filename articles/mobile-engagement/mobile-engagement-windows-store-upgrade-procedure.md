<properties 
    pageTitle="Procedimentos para a atualização do Windows SDK de aplicações Universal" 
    description="Aplicações Universal do Windows SDK atualizar procedimentos para Azure Cativação móvel"                     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procedimentos para a atualização do Windows SDK de aplicações Universal

Se já tiver o integrado uma versão anterior do Cativação para a aplicação, tem de ter em conta os seguintes pontos ao atualizar o SDK.

Poderá ter de seguir vários procedimentos se perdeu várias versões do SDK. Por exemplo, se migrar a partir do 0.10.1 para 0.11.0 que tem de pela primeira vez, siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

##<a name="from-330-to-340"></a>A partir do 3.3.0 para 3.4.0

### <a name="test-logs"></a>Registos de teste

Registos de consola produzidos pelo SDK agora podem ser ativado/desactivado/filtrados. Para personalizar isto, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um do valor disponível a partir de `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Recursos

Sobreposição de atingir foi melhorada. É parte dos recursos de pacote SDK NuGet.

Enquanto atualizar para a nova versão do SDK, pode escolher se pretende manter os seus ficheiros existentes a partir da pasta de sobreposição dos seus recursos ou não:

* Se a sobreposição anterior está a trabalhar para si ou estar a integrar o `WebView` elementos manualmente, em seguida, pode optar por manter os seus ficheiros Designing, continuará a funcionar. 
* Se pretender que Atualize para a nova sobreposição, basta substituir a totalidade `overlay` pasta a partir do seu recursos pelo novo a partir do pacote SDK (UWP aplicações: após a atualização, pode obter a nova pasta de sobreposição do perfil de utilizador %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Utilizar a nova sobreposição irá substituir todas as personalizações efetuadas na versão anterior.

##<a name="from-320-to-330"></a>A partir do 3.2.0 para 3.3.0

### <a name="resources"></a>Recursos
Este passo se refira apenas recursos personalizados. Se tiver personalizado os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, tem de cópia de segurança-las antes de atualizar e volte a aplicar a personalização de recursos atualizados.

##<a name="from-310-to-320"></a>A partir do 3.1.0 para 3.2.0

### <a name="resources"></a>Recursos
Este passo se refira apenas recursos personalizados. Se tiver personalizado os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, tem de cópia de segurança-las antes de atualizar e volte a aplicar a personalização de recursos atualizados.

### <a name="webview-integration"></a>Integração de vista da Web
Algumas melhorias para corresponder ao factores de forma de dispositivo diferente foram introduzidas nesta versão. Certifique-se de que a integração da vista de Web corresponde ao seguinte:

No seu XAML página (.):

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

E no seu ficheiro. cs associado:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
            
              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }
              
              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif
            
              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }
            
              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

##<a name="from-200-to-300"></a>A partir do 2.0.0 para 3.0.0

### <a name="resources"></a>Recursos
Este passo se refira apenas recursos personalizados. Se tiver personalizado os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, tem de cópia de segurança-las antes de atualizar e volte a aplicar a personalização de recursos atualizados.

##<a name="from-111-to-200"></a>A partir do 1.1.1 para 2.0.0

A seguinte tabela descreve como migrar uma integração SDK de serviço Capptain oferecido pelo Capptain SA para uma aplicação tecnologia do Azure Mobile Cativação. 

> [Azure.IMPORTANT] Capptain e Mobile Cativação não são os mesmos serviços e o procedimento indicado abaixo realça apenas como migrar a aplicação de cliente. Migrar o SDK na aplicação irá não migrar os dados a partir os servidores de Capptain para os servidores de Cativação Mobile

Se estiver a migrar de uma versão anterior, consulte o web site da Capptain migrar para o 1.1.1 pela primeira vez, em seguida, aplique o procedimento seguinte

### <a name="nuget-package"></a>Pacote Nuget

Substitua **Capptain.WindowsPhone** ao **MicrosoftAzure.MobileEngagement** Nuget pacote.

### <a name="applying-mobile-engagement"></a>Aplicar Cativação móvel

O SDK utiliza o termo `Engagement`. Tem de atualizar o seu projeto para que correspondam a esta alteração.

Tem de desinstalar o seu pacote de nuget Capptain atual. Considere esse serão removidas todas as suas alterações na pasta Capptain recursos. Se pretender manter os ficheiros em seguida, faça uma cópia dos mesmos.

Após esta ação, instale o pacote de nuget de Cativação do Microsoft Azure novo no projeto. Pode encontrá-lo diretamente no [nuget site]. ou índice aqui. Esta ação substitui todos os ficheiros de recursos utilizados pelo Cativação e adiciona a nova DLL de Cativação para as referências do projecto.

Tem de limpar as referências do projecto eliminando Capptain DLL referências. Se não faça isto, a versão do Capptain entram em conflito e erros irão acontecer.

Se tiver personalizado Capptain recursos, copie o conteúdo de ficheiros antigos e cole-as na novos ficheiros Cativação. Tenha em atenção que ficheiros xaml e cs tem de ser atualizados.

Quando esses passos são concluídos só tem de substituir as referências Capptain antigas por novas referências de Cativação.

1. Todos os espaços de nomes de Capptain tem de ser atualizados.

    Antes da migração:
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Após a migração:
    
        using Microsoft.Azure.Engagement;

2. Todas as categorias de Capptain que contêm "Capptain" deverão conter "Cativação".

    Antes da migração:
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Após a migração:
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Para os ficheiros xaml Capptain espaço de nomes e os atributos também alterar.

    Antes da migração:
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Após a migração:
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Alterações à página de sobreposição
    > [AZURE.IMPORTANT] Sobreposição também é alterada. É o seu novo espaço de nomes `Microsoft.Azure.Engagement.Overlay`. Tem de ser utilizados em ficheiros xaml e cs. Além disso `CapptainGrid` deve ter o nome `EngagementGrid`, `capptain_notification_content` e `capptain_announcement_content` são com nome `engagement_notification_content` e `engagement_announcement_content`.
    
    Para sobreposição:
    
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
    
    Transforma-se:
    
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>

5. Para os outros recursos, como Capptain imagens e ficheiros HTML, tenha em atenção que estes também nomes foram mudados para utilizar "Cativação".

### <a name="project-declaration"></a>Declaração de projeto

No Package.appxmanifest `File Type Associations` foi atualizado a partir de:

 -   capptain\_atinja\_conteúdo para Cativação\_atinja\_conteúdo
 -   capptain\_registo\_ficheiro para Cativação\_registo\_ficheiro

### <a name="application-id--sdk-key"></a>ID da aplicação / SDK chave

Cativação utiliza uma cadeia de ligação. Não tem de especificar um ID da aplicação e uma tecla SDK com Cativação Mobile, só tem de especificar uma cadeia de ligação. Pode configurar-o no seu ficheiro EngagementConfiguration.

A configuração de Cativação pode ser definida no seu `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar:

-   A cadeia de ligação de aplicação entre etiquetas `<connectionString>` e `<\connectionString>`.

Se pretender especificá-la o tempo de execução, pode ligar o método seguinte antes da inicialização do agente de Cativação:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

A cadeia de ligação para a aplicação é apresentada no Portal clássica do Azure.

### <a name="items-name-change"></a>Alteração de nome de itens

Todos os itens com nome *capptain* tem sido atribuídos *Cativação*. Da mesma forma para *Capptain* para *Cativação*.

Exemplos de utilizadas mais frequentemente Capptain itens:

-   CapptainConfiguration agora denominada EngagementConfiguration
-   CapptainAgent agora denominada EngagementAgent
-   CapptainReach agora denominada EngagementReach
-   CapptainHttpConfig agora denominada EngagementHttpConfig
-   GetCapptainPageName agora denominada GetEngagementPageName

Tenha em atenção que mudar o nome de também afeta sobreposto métodos.

 
