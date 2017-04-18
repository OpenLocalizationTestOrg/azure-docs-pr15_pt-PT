<properties 
    pageTitle="Telefone Silverlight Cativação SDK integração com o Windows" 
    description="Como integrar o Azure Cativação móvel com o Windows Phone Silverlight aplicações"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-engagement-sdk-integration"></a>Telefone Silverlight Cativação SDK integração com o Windows

> [AZURE.SELECTOR] 
- [Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Este procedimento descreve a forma mais simples para ativar do Azure Mobile Cativação e de análise monitorização de funções na sua aplicação do Windows Phone Silverlight.

Os passos seguintes são suficiente para ativar o relatório de registos necessários para calcular todas as estatísticas sobre utilizadores, sessões, atividades, falhas e Technicals. O relatório de registos necessários para calcular outras estatísticas como tarefas, erros e eventos têm de ser efetuado manualmente utilizando a API Cativação (consulte o artigo [como utilizar o Cativação Mobile avançadas etiquetagem API na sua aplicação do Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) ) uma vez que estas estatísticas são dependentes da aplicação.

##<a name="supported-versions"></a>Versões suportadas

O Mobile Cativação SDK para Windows Silverlight só pode ser integrado em aplicações de filtragem:

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE] Se está a filtrar o Windows Phone 8.1 (que não sejam Silverlight), em seguida, consulte ao [procedimento de integração com o Windows Universal](mobile-engagement-windows-store-integrate-engagement.md).

##<a name="install-the-mobile-engagement-silverlight-sdk"></a>Instalar o SDK do Silverlight Cativação móvel

O Mobile Cativação SDK para Windows o Silverlight está disponível como um pacote de Nuget denominado *MicrosoftAzure.MobileEngagement*. Pode instalá-lo a partir do Visual Studio Nuget pacote Manager. 

##<a name="add-the-capabilities"></a>Adicionar as capacidades

O SDK Cativação precisa de algumas funcionalidades do Windows Phone Silverlight SDK para poder funcione corretamente.

Abrir o `WMAppManifest.xml` ficheiro e certifique-se de que as seguintes capacidades são declaradas na `Capabilities` painel:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##<a name="initialize-the-engagement-sdk"></a>Iniciar a Cativação SDK

### <a name="engagement-configuration"></a>Configuração de Cativação

A configuração de Cativação é centralizada na `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar:

-   A cadeia de ligação de aplicação entre etiquetas `<connectionString>` e `<\connectionString>`.

Se pretender especificá-la o tempo de execução, pode ligar o método seguinte antes da inicialização do agente de Cativação:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

A cadeia de ligação para a aplicação é apresentada no Portal clássica do Azure.

### <a name="engagement-initialization"></a>Inicialização Cativação

Quando cria um novo projeto, um `App.xaml.cs` o ficheiro é gerado. Esta classe herda da `Application` e contém muitos dos métodos importantes. Também será utilizada para iniciar o SDK Cativação.

Modificar a `App.xaml.cs`:

-   Adicionar a sua `using` declarações:

        using Microsoft.Azure.Engagement;

-   Inserir `EngagementAgent.Instance.Init` na `Application_Launching` método:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
          EngagementAgent.Instance.Init();
        }

-   Inserir `EngagementAgent.Instance.OnActivated` na `Application_Activated` método:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
        }

> [AZURE.WARNING] Recomendamos vivamente impedir para adicionar a inicialização Cativação noutro local da sua aplicação. No entanto, tenha em atenção que a `EngagementAgent.Instance.Init` método é executado num tópico dedicado e não no tópico de IU.

##<a name="basic-reporting"></a>Relatório básico

### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Recomendado método: Sobrecarga seu `PhoneApplicationPage` classes

Para ativar o relatório de todos os registos necessários por Cativação para calcular os utilizadores, sessões, atividades, falhas e estatísticas técnicas, basta fazer com que todas as sua `PhoneApplicationPage` sub-classes herdam a partir do `EngagementPage` aulas.

Eis um exemplo de como fazê-lo para uma página da sua aplicação. O que pode fazer a mesma coisa para todas as páginas da sua aplicação.

#### <a name="c-source-file"></a>Ficheiro de origem c#

Modificar a sua página `.xaml.cs` ficheiro:

-   Adicionar a sua `using` declarações:

        using Microsoft.Azure.Engagement;

-   Substituir `PhoneApplicationPage` com `EngagementPage` :

**Sem Cativação:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Com Cativação:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.WARNING] Se a sua página herda as `OnNavigatedTo` método, tenha cuidado para permitir que o `base.OnNavigatedTo(e)` de chamadas. Caso contrário, não vai ser comunicada a atividade. Verdade, o `EngagementPage` está a chamar `StartActivity` dentro de `OnNavigatedTo` método.

#### <a name="xaml-file"></a>Ficheiro XAML

Modificar a sua página `.xaml` ficheiro:

-   Adicione a sua declarações de espaços de nomes:

        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Substituir `phone:PhoneApplicationPage` com `engagement:EngagementPage` :

**Sem Cativação:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Com Cativação:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
        
            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Substituir o comportamento predefinido

Por predefinição, o nome de classe da página é comunicado como o nome de atividade, com sem extra. Se a classe utiliza sufixo "Página", Cativação também removerá-lo.

Se pretender substituir o comportamento predefinido para o nome, basta adicioná isto ao seu código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Se pretender comunicar algumas informações adicionais com a sua atividade, pode adicionar este para o seu código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Estes métodos chamam-se a partir da `OnNavigatedTo` método da sua página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: chamar `StartActivity()` manualmente

Se não conseguir ou não pretende que seja sobrecarga seu `PhoneApplicationPage` classes, em vez disso, pode começar a suas atividades ao contactar o suporte `EngagementAgent` métodos diretamente.

Recomendamos que estiver a ligar `StartActivity` dentro da sua `OnNavigatedTo` método do seu PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [AZURE.IMPORTANT] Certifique-se de que termina a sua sessão corretamente.
>
> O SDK chama-se automaticamente o `EndActivity` método quando a aplicação está fechada. Assim, é **vivamente** recomendado para ligar para o `StartActivity` método sempre que a atividade do utilizador alterar e, para **nunca** ligar o `EndActivity` método. Este método envia uma mensagem no servidor de Cativação que o utilizador actual saiu da aplicação e este impactos todos os registos de aplicação.

##<a name="advanced-reporting"></a>Avançadas de elaboração de relatórios

Opcionalmente, poderá querer eventos específicos da aplicação do relatório, erros e tarefas, para fazê-lo, utilize os outros métodos que se encontram na `EngagementAgent` escolares. Permite a API de Cativação para utilizar todas as funcionalidades avançadas de Cativação.

Para obter mais informações, consulte o artigo [como utilizar o Cativação Mobile avançadas etiquetagem API na sua aplicação do Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuração avançada

### <a name="disable-automatic-crash-reporting"></a>Desactivar o relatório de falha de sistema automática

Pode desativar a falha automática funcionalidade de Cativação de elaboração de relatórios. Em seguida, quando irá ocorrer uma exceção não processada, Cativação não fazer nada.

> [AZURE.WARNING] Se planeia desativar esta funcionalidade, tenha em atenção de que quando irá ocorrer uma falha de sistema não processada na sua aplicação, Cativação não enviar a falhar **e** não irá fechar sessão e tarefas de implementação.

Para desativar falha automática relatórios, personalize apenas a configuração dependendo da forma que declarado como:

#### <a name="from-engagementconfigurationxml-file"></a>A partir do `EngagementConfiguration.xml` ficheiro

Configurar falha de sistema do relatório para `false` entre `<reportCrash>` e `</reportCrash>` etiquetas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>A partir do `EngagementConfiguration` objeto em tempo de execução

Defina falha de sistema do relatório para FALSO utilizando o objeto EngagementConfiguration.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Modo de rajada

Por predefinição, os relatórios de serviço de Cativação inicia em tempo real. Se a sua aplicação relatórios registos muito frequentemente, é melhor para os registos de memória intermédia e reportá-las todas ao mesmo tempo um tempo regular base (esta opção é denominada "modo de rajada").

Para fazê-lo, contacte o método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. Em qualquer altura, se pretender reativar o registo em tempo real, basta chame o método sem qualquer parâmetro ou em conjunto com o valor de 0.

O modo de rajada ligeiramente aumentar a vida de baterias, mas tem o impacto no Monitor Cativação: todos os duração sessões e tarefas de implementação será arredondada para o limiar de rajada (portanto, sessões e tarefas mais pequena do que o limiar de rajada poderão não estar visível). Recomenda-se para utilizar um limite de rajada já não que 30000 (30s). Tem de ter em mente que registos guardados estão limitados a 300 itens. Se enviar é demasiado longa pode perder algumas registos.

> [AZURE.WARNING] O limiar de rajada não pode ser configurado para um período menor que um segundo. Se tentar fazê-lo, o SDK mostrará um rastreio com o erro e será automaticamente repor para o valor predefinido, ou seja, igual a zero segundos. Isto irá acionar o SDK para comunicar os registos em tempo real.
 
