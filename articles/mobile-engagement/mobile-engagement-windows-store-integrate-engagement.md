<properties 
    pageTitle="Integração com o Windows aplicações Universal Cativação SDK" 
    description="Como integrar o Azure Cativação móvel com aplicações Universal do Windows"                  
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

# <a name="windows-universal-apps-engagement-sdk-integration"></a>Integração com o Windows aplicações Universal Cativação SDK

> [AZURE.SELECTOR] 
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Este procedimento descreve a forma mais simples para ativar do Cativação e de análise monitorização de funções na sua aplicação Universal do Windows.

Os passos seguintes são suficiente para ativar o relatório de registos necessários para calcular todas as estatísticas sobre utilizadores, sessões, atividades, falhas e Technicals. O relatório de registos necessários para calcular outras estatísticas como tarefas, erros e eventos têm de ser efetuado manualmente utilizando a API Cativação (consulte o artigo [como utilizar o Cativação Mobile avançadas etiquetagem API na sua aplicação do Windows Universal](mobile-engagement-windows-store-use-engagement-api.md) uma vez que estas estatísticas são dependentes das aplicações.

## <a name="supported-versions"></a>Versões suportadas

Só pode ser integradas Mobile Cativação SDK para Windows Universal aplicações em runtime do Windows e de aplicações Universal plataforma Windows filtragem:

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (famílias de ambiente de trabalho e móveis)

> [AZURE.NOTE] Se está a filtrar o Windows Phone Silverlight, referir-se para o [procedimento de integração com o Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Instalar o SDK aplicações Universal Cativação móvel

### <a name="all-platforms"></a>Todas as plataformas

A Cativação SDK para Windows Universal aplicação Mobile está disponível como um pacote de Nuget denominado *MicrosoftAzure.MobileEngagement*. Pode instalá-lo a partir do Visual Studio Nuget pacote Manager.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8 e Windows Phone 8.1

NuGet implementa automaticamente os recursos SDK a `Resources` pasta na raiz do seu projeto de aplicação.

### <a name="windows-10-universal-windows-platform-applications"></a>Aplicações do Windows 10 Universal plataforma do Windows

NuGet não implementar os recursos SDK na sua aplicação UWP ainda automaticamente. Tem de fazê-lo manualmente até que a implementação de recursos é reintroduzida em NuGet:

1.  Abra o Explorador de ficheiros.
2.  Navegue para a seguinte localização (**x.x.x** é a versão do Cativação estiver a instalar): *% de perfil de utilizador\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  Arraste e largue a pasta de **recursos** a partir do Explorador de ficheiros na raiz do seu projeto no Visual Studio.
4.  No Visual Studio selecionar o seu projeto e ativar o ícone **Mostrar todos os ficheiros** na parte superior do **Explorador de soluções**.
5.  Alguns ficheiros não são incluídos no projeto. Para importá-los ao mesmo tempo botão direito do rato clique na pasta de **recursos** , **excluir do projeto** , em seguida, clique outro direito do rato na pasta de **recursos** , **incluir no project** para incluir voltar a pasta inteira. Todos os ficheiros a partir da pasta de **recursos** estão agora incluídos no projeto.

## <a name="add-the-capabilities"></a>Adicionar as capacidades

O SDK Cativação precisa de algumas funcionalidades do Windows SDK para poder funcione corretamente.

Abrir o `Package.appxmanifest` ficheiro e certifique-se de que as seguintes capacidades sejam declaradas:

-   `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Iniciar a Cativação SDK

### <a name="engagement-configuration"></a>Configuração de Cativação

A configuração de Cativação é centralizada na `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar:

-   A cadeia de ligação de aplicação entre etiquetas `<connectionString>` e `<\connectionString>`.

Se pretender especificá-la o tempo de execução, pode ligar o método seguinte antes da inicialização do agente de Cativação:
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

A cadeia de ligação para a aplicação é apresentada no Portal clássica do Azure.

### <a name="engagement-initialization"></a>Inicialização Cativação

Quando cria um novo projeto, um `App.xaml.cs` o ficheiro é gerado. Esta classe herda da `Application` e contém muitos dos métodos importantes. Também será utilizada para iniciar o SDK Cativação.

Modificar a `App.xaml.cs`:

-   Adicionar a sua `using` declarações:

        using Microsoft.Azure.Engagement;

-   Defina um método para partilhar a inicialização Cativação uma vez para todas as chamadas:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
        
          // or
        
          EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Chamar `InitEngagement` na `OnLaunched` método:

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   Quando a aplicação é iniciada utilizando um esquema personalizado, outra aplicação ou a linha de comandos, em seguida, o `OnActivated` método é chamado. Também precisa de iniciar o SDK Cativação quando a sua aplicação está ativada. Para fazê-lo, substituir `OnActivated` método:

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }

> [AZURE.IMPORTANT] Recomendamos vivamente impedir para adicionar a inicialização Cativação noutro local da sua aplicação.

## <a name="basic-reporting"></a>Relatório básico

### <a name="recommended-method-overload-your-page-classes"></a>Recomendado método: Sobrecarga seu `Page` classes

Para ativar o relatório de todos os registos necessários por Cativação para calcular os utilizadores, sessões, atividades, falhas e estatísticas técnicas, basta fazer com que todas as sua `Page` sub-classes herdam a partir do `EngagementPage` aulas.

Eis um exemplo de como fazê-lo para uma página da sua aplicação. O que pode fazer a mesma coisa para todas as páginas da sua aplicação.

#### <a name="c-source-file"></a>Ficheiro de origem c#

Modificar a sua página `.xaml.cs` ficheiro:

-   Adicionar a sua `using` declarações:

        using Microsoft.Azure.Engagement;

-   Substituir `Page` com `EngagementPage`:

**Sem Cativação:**
    
        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Com Cativação:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] Se a sua página substitui o `OnNavigatedTo` método, certifique-se de que chamadas `base.OnNavigatedTo(e)`. Caso contrário, não vai ser comunicada a atividade (o `EngagementPage` chamadas `StartActivity` dentro da sua `OnNavigatedTo` método).

#### <a name="xaml-file"></a>Ficheiro XAML

Modificar a sua página `.xaml` ficheiro:

-   Adicione a sua declarações de espaços de nomes:

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Substituir `Page` com `engagement:EngagementPage`:

**Sem Cativação:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Com Cativação:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Substituir o comportamento predefinido

Por predefinição, o nome de classe da página é comunicado como o nome de atividade, com sem extra. Se a classe utiliza o sufixo "Página", Cativação também removerá-lo.

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

Se não conseguir ou não pretende que seja sobrecarga seu `Page` classes, em vez disso, pode começar a suas atividades ao contactar o suporte `EngagementAgent` métodos diretamente.

Recomendamos que para ligar para `StartActivity` dentro da sua `OnNavigatedTo` método da sua página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Certifique-se de que termina a sua sessão corretamente.
> 
> Liga automaticamente para o Windows SDK Universal a `EndActivity` método quando a aplicação está fechada. Assim, é **vivamente** recomendado para ligar para o `StartActivity` método sempre que a atividade do utilizador alterar e, para **nunca** ligar o `EndActivity` método, este método envia Cativação servidor que o utilizador atual tem deixe a aplicação, isto irá impactos todos os registos de aplicação.

## <a name="advanced-reporting"></a>Avançadas de elaboração de relatórios

Opcionalmente, poderá querer eventos específicos da aplicação do relatório, erros e tarefas, para fazê-lo, utilize os outros métodos que se encontram na `EngagementAgent` escolares. Permite a API de Cativação para utilizar todas as funcionalidades avançadas de Cativação.

Para obter mais informações, consulte o artigo [como utilizar o Cativação Mobile avançadas etiquetagem API na sua aplicação do Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuração avançada

### <a name="disable-automatic-crash-reporting"></a>Desactivar o relatório de falha de sistema automática

Pode desativar a falha automática funcionalidade de Cativação de elaboração de relatórios. Em seguida, quando irá ocorrer uma exceção não processada, Cativação não fazer nada.

> [AZURE.WARNING] Se planeia desativar esta funcionalidade, tenha em atenção de que quando irá ocorrer uma falha de sistema não processada na sua aplicação, Cativação não enviará que a falhar **e** não irá fechar sessão e tarefas de implementação.

Para desativar falha automática relatórios, personalize apenas a configuração dependendo da forma que declarados-lo:

#### <a name="from-engagementconfigurationxml-file"></a>A partir do `EngagementConfiguration.xml` ficheiro

Configurar falha de sistema do relatório para `false` entre `<reportCrash>` e `</reportCrash>` etiquetas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>A partir do `EngagementConfiguration` objeto em tempo de execução

Defina falha de sistema do relatório para FALSO utilizando o objeto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Modo de rajada

Por predefinição, os relatórios de serviço de Cativação inicia em tempo real. Se a sua aplicação relatórios registos muito frequentemente, é melhor para os registos de memória intermédia e reportá-las todas ao mesmo tempo um tempo regular base (esta opção é denominada "modo de rajada").

Para fazê-lo, contacte o método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. Em qualquer altura, se pretender reativar o registo em tempo real, basta chame o método sem qualquer parâmetro ou em conjunto com o valor de 0.

O modo de rajada ligeiramente aumentar a vida de baterias, mas tem o impacto no Monitor Cativação: todos os duração sessões e tarefas de implementação será arredondada para o limiar de rajada (portanto, sessões e tarefas mais pequena do que o limiar de rajada poderão não estar visível). Recomenda-se para utilizar um limite de rajada já não que 30000 (30s). Tem de ter em mente que registos guardados estão limitados a 300 itens. Se enviar é demasiado longa pode perder algumas registos.

> [AZURE.WARNING] O limiar de rajada não pode ser configurado para um período menor que 1s. Se tentar fazê-lo, o SDK mostrará um rastreio com o erro e será automaticamente repor para o valor predefinido, ou seja, 0s. Isto irá acionar o SDK para comunicar os registos em tempo real.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
 
