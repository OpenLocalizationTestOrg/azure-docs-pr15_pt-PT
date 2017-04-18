<properties 
    pageTitle="Integração do Silverlight alcance SDK do Windows Phone" 
    description="Como integrar o Azure Cativação móvel alcance com aplicações do Windows Phone Silverlight"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-reach-sdk-integration"></a>Integração do Silverlight alcance SDK do Windows Phone

Tem de seguir o procedimento de integração descrito a [Integração do Windows Phone Silverlight Cativação SDK](mobile-engagement-windows-phone-integrate-engagement.md) antes de seguir este guia.

##<a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Incorporar o SDK do atingir compromisso no seu projeto do Windows Phone Silverlight

Não possui alguma coisa para adicionar. `EngagementReach`referências e recursos já estão no projeto.

> [AZURE.TIP]  Pode personalizar imagens localizadas na `Resources` pasta do projeto, sobretudo no ícone de marca corporativa (predefinição para o ícone de Cativação).

##<a name="add-the-capabilities"></a>Adicionar as capacidades

O SDK do atinja Cativação precisa de algumas funcionalidades adicionais.

Abrir o `WMAppManifest.xml` ficheiro e certifique-se de que as seguintes capacidades sejam declaradas:

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

A primeira parte é utilizada pelo serviço MPNS para permitir que a apresentação de notificação de alerta. A segunda opção é utilizada para incorporar uma tarefa de browser no SDK.

Editar o `WMAppManifest.xml` ficheiro e adicionar dentro de `<Capabilities />` etiqueta:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##<a name="enable-the-microsoft-push-notification-service"></a>Ativar o serviço de notificações Push da Microsoft

Para poder utilizar o **Serviço de notificações Push da Microsoft** (designado MPNS) seu `WMAppManifest.xml` ficheiro tem de ter um `<App />` marcar com um `Publisher` atributo definido para o nome do seu projeto.

##<a name="initialize-the-engagement-reach-sdk"></a>Iniciar o alcance Cativação SDK

### <a name="engagement-configuration"></a>Configuração de Cativação

A configuração de Cativação é centralizada na `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar a configuração de atingir:

-   *Opcional*, indicam se a push nativa (MPNS) está ativado ou não está entre `<enableNativePush>` e `</enableNativePush>` etiquetas, (`true` por predefinição).
-   *Opcional*, indicar o nome do canal push entre `<channelName>` e `</channelName>` códigos, fornecem o mesmo que a aplicação atualmente pode utilizar ou deixá-la vazia.

Se pretender especificá-la o tempo de execução, pode ligar o método seguinte antes da inicialização do agente de Cativação:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */
    
    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] Pode especificar o nome do canal MPNS push da sua aplicação. Por predefinição, Cativação cria um nome com base no appId. Não necessita de para especificar o nome de si, exceto se planeia utilizar o canal push fora Cativação.

### <a name="engagement-initialization"></a>Inicialização Cativação

Modificar a `App.xaml.cs`:

-   Adicionar a sua `using` declarações:

        using Microsoft.Azure.Engagement;

-   Inserir `EngagementReach.Instance.Init` apenas depois `EngagementAgent.Instance.Init` no `Application_Launching` :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

-   Inserir `EngagementReach.Instance.OnActivated` na `Application_Activated` método:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

> [AZURE.IMPORTANT] O `EngagementReach.Instance.Init` é executado num tópico dedicado. Não possui fazê-lo.

##<a name="app-store-submission-considerations"></a>Considerações sobre a aplicação da loja submissão

Microsoft impõe algumas regras quando utilizar as notificações push:

A partir de documentação Microsoft [Políticas de aplicação] , secção 2.9:

1) Tem de perguntar ao utilizador para aceitar a receber notificações push. Em seguida, nas definições do, adicione uma forma para desativar as notificações push.

O objeto EngagementReach fornece dois métodos para gerir o optar-in/optar-out, `EnableNativePush()` e `DisableNativePush()`. Pode, por exemplo, criar uma opção nas definições com um botão de alternar para desativar ou ativar MPNS.

Também pode decidir desativar MPNS através da configuração de Cativação\<windows phone-sdk-alcance-configuração\>.

> 2.9.1) a aplicação pela primeira vez deve descrever as notificações para ser fornecido e **obter a permissão de utilizador express (optar ativamente por participar-in)**e **tem de fornecer um mecanismo através do qual o utilizador pode optar por terminar receber notificações push**. Todas as notificações de fornecidos com o serviço de notificações de emissão Microsoft devem ser compatíveis com a descrição fornecida ao utilizador e devem cumprir todos aplicáveis [Políticas de aplicação]  [ Content Policies] e [Requisitos adicionais para tipos específicos de aplicação].

2) Não deve utilizar notificações push demasiadas. Cativação processará as notificações por si.

> 2.9.2) a aplicação e a sua utilização do serviço de notificações Push do Microsoft tem demasiado não utilizar a capacidade de rede ou largura de banda do serviço de notificações Push a Microsoft, ou caso contrário indevidamente burden num Windows Phone ou outro dispositivo Microsoft ou serviço com as notificações push em excesso, tal como determinada pela Microsoft no seu razoável entender e tem não danificar ou interferir com quaisquer redes Microsoft ou servidores ou qualquer servidores de terceiros ou redes ligadas para o serviço de notificações Push Microsoft.

3) Não confiar MPNS para enviar informações de criticals. Cativação utiliza MPNS, por isso, também se aplica esta regra para campanhas criadas dentro de Cativação front-end.

> 2.9.3) o serviço de notificações de emissão a Microsoft não pode ser utilizado para enviar notificações que estão missão crítico ou de outro podem afetar matéria de vida ou morte, incluindo, sem notificações crítico limitação relacionados com um dispositivo médico ou condição. MICROSOFT EXCLUI EXPRESSAMENTE QUAISQUER GARANTIAS QUE A UTILIZAÇÃO DO SERVIÇO DE NOTIFICAÇÕES PUSH DA MICROSOFT OU ENTREGA DAS NOTIFICAÇÕES DE SERVIÇO DE NOTIFICAÇÕES PUSH DA MICROSOFT ESTARÁ SEM INTERRUPÇÕES, ERRO GRATUITO OU DE OUTRO GARANTE A OCORRER NUMA BASE EM TEMPO REAL.

**Não podemos garantir que a sua aplicação irá passar o processo de validação se não respeitar estas recomendações.**

##<a name="handle-data-push-optional"></a>Alça de dados de emissão (opcionais)

Se pretender que a sua aplicação possam receber alcance puxa de dados, tem de implementar dois eventos a classe de EngagementReach de:

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

##<a name="customize-ui-optional"></a>Personalizar IU (opcional)

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

Em seguida, defina o conteúdo da `EngagementReach.Instance.Handler` campo com o objeto personalizado no seu `App.xaml.cs` dentro de classe a `Application_Launching` método.

**Exemplo de código:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [AZURE.NOTE] Por predefinição, Cativação utiliza a sua própria implementação do `EngagementReachHandler`. Não tem de criar o seu próprio e, se fazê-lo, não tem de substituir as cada método. O comportamento predefinido é selecionar o objeto de base de Cativação.

### <a name="layouts"></a>Esquemas

Por predefinição, atingir utilizará os recursos da DLL incorporados para apresentar as páginas e notificações.

No entanto, pode decidir utilizar os seus próprios recursos para refletir a sua marca nestes componentes.

Pode substituir `EngagementReachHandler` métodos no seu subclasse para indicar ao Cativação para utilizar os esquemas:

**Exemplo de código:**

    // In your subclass of EngagementReachHandler
    
    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetPollUri()
    {
       // return the path of your own xaml
    }
    
    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [AZURE.TIP] O `CreateNotification` método pode devolver nulo. A notificação não será apresentada e campanha alcance será ignorada.

Para simplificar a sua implementação de esquema, fornecemos também nossa própria xaml que pode servir de base para o seu código. Se encontrarem o arquivo de Cativação SDK (/ src/alcance /).

> [AZURE.WARNING] As fontes que fornecemos são exatos mesmo os que utilizamos. Por isso, se pretender modificá-las diretamente, não se esqueça alterar o espaço de nomes e o nome.

### <a name="notification-position"></a>Posição de notificação

Por predefinição, é apresentada uma notificação de na aplicação na parte inferior esquerdo da aplicação. Pode alterar este comportamento ao substituir a `GetNotificationPosition` método a `EngagementReachHandler` objeto.

    // In your subclass of EngagementReachHandler
    
    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Atualmente, pode escolher entre a `BOTTOM` (predefinição) e `TOP` posições.

### <a name="launch-message"></a>Mensagem de iniciação

Quando um utilizador clica numa notificação de sistema (uma alerta), Cativação inicia a aplicação, carregar o conteúdo das mensagens push e apresentar a página para a campanha correspondente.

Existe um atraso entre o lançamento da aplicação e a apresentação da página (consoante a velocidade da sua rede).

Para indicar ao utilizador que algo está a carregar, deve fornecer um informações visuais, como uma barra de progresso ou um indicador de progresso. Cativação não consegue processar que o próprio, mas fornece alguns processadores por si.

Para implementar a chamada de retorno, efetue:

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

Pode definir a chamada de retorno na sua `Application_Launching` método de seu `App.xaml.cs` de ficheiros, preferência antes o `EngagementReach.Instance.Init()` de chamadas.

> [AZURE.TIP] Processador de cada chama-se à IU do tópico. Não tem de se preocupar ao utilizar um MessageBox ou algo relacionadas com a interface de utilizador.

[Políticas de aplicação]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Requisitos adicionais para os tipos de aplicação específica]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 
