<properties 
    pageTitle="Integração com o Windows alcance de aplicações Universal SDK" 
    description="Como integrar o Azure Cativação móvel alcance com aplicações Universal do Windows"
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

# <a name="windows-universal-apps-reach-sdk-integration"></a>Integração com o Windows alcance de aplicações Universal SDK

Tem de seguir o procedimento de integração descrito a [Universal Cativação SDK integração com o Windows](mobile-engagement-windows-store-integrate-engagement.md) antes de seguir este guia.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Incorporar o SDK do atingir compromisso no seu projeto Universal do Windows

Não possui alguma coisa para adicionar. `EngagementReach`referências e recursos já estão no projeto.

> [AZURE.TIP] Pode personalizar imagens localizadas na `Resources` pasta do projeto, sobretudo no ícone de marca corporativa (predefinição para o ícone de Cativação). Na aplicações Universal também pode mover o `Resources` pasta no seu projeto partilhado para partilhar o seu conteúdo entre aplicações, mas terá de manter o `Resources\EngagementConfiguration.xml` na sua localização predefinida de ficheiros tal como está plataforma dependente.

## <a name="enable-the-windows-notification-service"></a>Ativar o serviço de notificação do Windows

### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8 e Windows Phone 8.1 apenas

Para poder utilizar o **Serviço de notificações do Windows** (designado WNS) no seu `Package.appxmanifest` de ficheiros no `Application UI` clique no `All Image Assets` na caixa bot para a esquerda. À direita da caixa de no `Notifications`, alterar `toast capable` a partir do `(not set)` para `(Yes)`.

### <a name="all-platforms"></a>Todas as plataformas

Tem de sincronizar a sua aplicação à sua conta Microsoft e para a plataforma de Cativação. Para este tem de criar uma conta ou inicie sessão no [Centro de Dev Center do windows](https://dev.windows.com). Após que criar uma nova aplicação e localize o SID e chave secreta. No frontend Cativação, aceda a sua definição de aplicação no `native push` e cole as suas credenciais. Após esta ação, clique com botão direito no seu projeto, selecione `store` e `Associate App with the Store...`. Só precisa de selecione a aplicação de ter criar antes de para sincronizá-la.

## <a name="initialize-the-engagement-reach-sdk"></a>Iniciar o alcance Cativação SDK

Modificar a `App.xaml.cs`:

-   Inserir `EngagementReach.Instance.Init` apenas depois `EngagementAgent.Instance.Init` no seu `InitEngagement` método:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
          EngagementReach.Instance.Init(e);
        }

    O `EngagementReach.Instance.Init` é executado num tópico dedicado. Não possui fazê-lo.

> [AZURE.NOTE] Se estiver a utilizar notificações push noutro local na sua aplicação, em seguida, tem de [partilhar o seu canal push](#push-channel-sharing) com atingir compromisso.

## <a name="integration"></a>Integração

Cativação disponibiliza duas formas de adicionar a atingir faixas de na aplicação e vistas intersticial para anúncios e consultas na sua aplicação: a integração de sobreposição e a integração de manual de vistas web. Não devem combinar ambos os abordagem na mesma página.

A escolha entre a integração de duas pode ser resumida da seguinte forma:

-   Pode escolher a integração de sobreposição se já herda as páginas do agente de `EngagementPage`, é apenas uma questão de substituição de `EngagementPage` por `EngagementPageOverlay` e `xmlns:engagement="using:Microsoft.Azure.Engagement"` por `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` nas suas páginas.
-   Pode escolher as vistas de web integração manual se pretende colocar com precisão a IU atinja dentro de páginas ou se não pretender adicionar outro nível de herança às suas páginas. 

### <a name="overlay-integration"></a>Integração de sobreposição

Sobreposição de Cativação dinamicamente adiciona os elementos de interface de utilizador utilizados para apresentar campanhas de atingir na sua página. Se a sobreposição que se adaptem às não seu esquema deve considerar as vistas de web integração manual em vez disso.

Na sua alteração de ficheiro .xaml `EngagementPage` referência a`EngagementPageOverlay`

-   Adicione a sua declarações de espaços de nomes:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Substituir `engagement:EngagementPage` com `engagement:EngagementPageOverlay`:

**Com EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
        
            <!-- Your layout -->
        </engagement:EngagementPage>

**Com EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
        
            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Em seguida, no seu ficheiro. cs marcar a sua página no `EngagementPageOverlay` em vez de `EngagementPage` e importar `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

-   Substituir `EngagementPage` com `EngagementPageOverlay`:

**Com EngagementPage:**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Com EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Sobreposição de Cativação adiciona uma `Grid` elemento na parte superior a sua página composto pelo esquema e as duas `WebView` elementos um para a faixa e a outra para a vista intersticial.

Pode personalizar os elementos de sobreposição em diretamente a `EngagementPageOverlay.cs` ficheiro.

### <a name="web-views-manual-integration"></a>Integração do Web vistas manual

Alcance irão procurar as páginas para as duas `WebView` elementos responsáveis para apresentar a faixa e a vista intersticial. A única coisa que tem de fazer consiste em adicionar esses duas `WebView` elementos num local nas suas páginas, eis um exemplo:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


Neste exemplo a `WebView` elementos são esticados para se ajustar os respetivos contentor que automaticamente tamanhos-los novamente em caso de alteração de tamanho de rotação ou janela do ecrã.

> [AZURE.WARNING] É importante manter a mesma atribuição de nomes `engagement_notification_content` e `engagement_announcement_content` para o `WebView` elementos. Alcance é identificá-los ao seu nome. 

## <a name="handle-datapush-optional"></a>Alça de datapush (opcional)

Se pretender que a sua aplicação possam receber alcance puxa de dados, tem de implementar dois eventos a classe de EngagementReach de:

No App.xaml.cs no construtor App() adicione:

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };
            
            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Pode ver que a chamada de retorno de cada método devolve um valor booleano. Cativação envia feedback a back-end após distribuir o push de dados. Se a chamada de retorno devolve falsa, o `exit` comentários será enviada. Caso contrário, será `action`. Se não chamada de retorno estiver definida para os eventos, o `drop` Cativação serão devolvido comentários.

> [AZURE.WARNING] Cativação não é possível receber respostas de múltiplos para um push de dados. Se planeia configurar vários processadores num evento, tenha em atenção que o comentários corresponderão para a última um enviada. Neste caso, recomendamos que devolve sempre o mesmo valor para evitar ter confuso comentários na front-end.

## <a name="customize-ui-optional"></a>Personalizar IU (opcional)

### <a name="first-step"></a>Primeiro passo

Vamos permitem-lhe personalizar o alcance IU.

Para fazê-lo, tem de criar uma subclasse do `EngagementReachHandler` escolares.

**Exemplo de código:**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Em seguida, defina o conteúdo da `EngagementReach.Instance.Handler` campo com o objeto personalizado no seu `App.xaml.cs` dentro de classe a `App()` método.

**Exemplo de código:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [AZURE.NOTE]Por predefinição, Cativação utiliza a sua própria implementação do `EngagementReachHandler`.
> Não tem de criar o seu próprio e, se fazê-lo, não tem de substituir as cada método. O comportamento predefinido é selecionar o objeto de base de Cativação.

### <a name="web-view"></a>Vista da Web

Por predefinição, atingir utilizará os recursos da DLL incorporados para apresentar as páginas e notificações.

Para fornecer uma personalização completa possibilidades utilizamos apenas vista da web. Se quiser personalizar esquemas, diretamente substituir os ficheiros de recursos `EngagementAnnouncement.html` e `EngagementNotification.html`. Precisa de Cativação todo o código no `<body></body>` para ser executado corretamente. Mas pode adicionar etiqueta externa `engagement_webview_area`.

No entanto, pode optar por utilizar os seus próprios recursos.

Pode substituir `EngagementReachHandler` métodos no seu subclasse para indicar ao Cativação para utilizar os esquemas, mas encarregam-se para incorporado o mecanismo Cativação:

**Exemplo de código:**
            
            // In your subclass of EngagementReachHandler
            
            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Por predefinição, é AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`. Representa o ficheiro html que estruturar o conteúdo de uma mensagem de push (anúncio de nascimento de texto, Web anoucement e anúncio de nascimento de inquérito). É AnnouncementName `engagement_announcement_content`. É o nome do design vista da Web na sua página de xaml.

É NotfificationHTML `ms-appx-web:///Resources/EngagementNotification.html`. Representa o ficheiro html que a notificação de uma mensagem de push de estrutura. É NotfificationName `engagement_notification_content`. É o nome do design vista da Web na sua página de xaml.

### <a name="customization"></a>Personalização

Pode personalizar a notificação e anúncio tem de vista da web que pretende se preservar o objeto de Cativação. Tomar cuidado essa vista Web objeto é descrito três vezes - a primeira vez no seu xaml, segunda vez no seu ficheiro. cs o método "setwebview()" e terceira hora no ficheiro html.

-   No seu xaml descrevem o componente de vista da Web atual do esquema de gráfico.
-   No seu ficheiro. cs pode definir "setwebview()" na qual define a dimensão das duas vista da Web (notificação, anúncio). É muito eficaz quando a aplicação é redimensionar.
-   No ficheiro html Cativação descrevemos o conteúdo de vista da Web, estrutura e as posições elementos entre si.

### <a name="launch-message"></a>Mensagem de iniciação

Quando um utilizador clica numa notificação de sistema (uma alerta), Cativação inicia a aplicação, carregar o conteúdo das mensagens push e apresentar a página para a campanha correspondente.

Existe um atraso entre o lançamento da aplicação e a apresentação da página (consoante a velocidade da sua rede).

Para indicar ao utilizador que algo está a carregar, deve fornecer um informações visuais, como uma barra de progresso ou um indicador de progresso. Cativação não consegue processar que o próprio, mas fornece alguns processadores por si.

Para implementar a chamada de retorno, no App.xaml.cs no "App() público {}" Adicione:

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
            
            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
            
            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Que pode definir a chamada de retorno no seu método de "App() público {}" do seu `App.xaml.cs` de ficheiros, preferência antes de `EngagementReach.Instance.Init()` chamada.

> [AZURE.TIP] Processador de cada chama-se à IU do tópico. Não tem de se preocupar ao utilizar um MessageBox ou algo relacionadas com a interface de utilizador.

##<a id="push-channel-sharing"></a>Partilha de canal de emissão

Se estiver a utilizar notificações push para outro efeito na sua aplicação, em seguida, tem de utilizar o canal push funcionalidade do SDK Cativação de partilha. Este é para evitar push perdida.

- Pode fornecer o seu próprio canal push para a inicialização atingir compromisso. O SDK irá utilizá-lo em vez de pedir um novo.

Atualizar a inicialização Cativação atinja com o seu canal push da `InitEngagement` método a partir de `App.xaml.cs` ficheiro:
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

- Em alternativa, se pretender consumir o canal push após a inicialização alcance, em seguida, que pode definir uma chamada de retorno no Cativação atinja para obter o canal push uma vez é criada pelo SDK.

Defina o seu chamada de retorno em qualquer local **depois da** inicialização alcance:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Sugestão de esquema personalizado

Fornecemos utilização de esquema personalizado. Pode enviar o tipo de URI diferente a partir do frontend Cativação para ser utilizado na sua aplicação de Cativação. Esquema predefinido como `http, ftp, ...` são gerir pelo Windows, irá uma janela linha de comandos, não se estiverem nenhuma aplicação predefinida instalada no dispositivo. Também pode criar o seu próprio esquema personalizado para a sua aplicação.

A forma simple para definir um esquema personalizado na sua aplicação é abrir o seu `Package.appxmanifest` aceda no `Declarations` painel. Selecione `Protocol` nas declarações disponíveis desloque-se a caixa e adicioná-la. Editar o `Name` nome do campo com o seu novo protocolo desejado.

Agora para utilizar este protocolo, edite o `App.xaml.cs` com o `OnActivated` método e não se esqueça de inicialização Cativação aqui também:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);
            
              //TODO design action to do when app is launch
            
              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
            
                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion
 
