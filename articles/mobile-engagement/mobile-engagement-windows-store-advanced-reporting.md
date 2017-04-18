<properties
    pageTitle="Windows Universal avançada de elaboração de relatórios com MobileApps Cativação"
    description="Como integrar o Azure Cativação móvel com aplicações Universal do Windows"                  
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>As avançadas de relatório com a Cativação de aplicações Universal do Windows SDK

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-advanced-reporting.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Este tópico descreve cenários de elaboração de relatórios adicionais na sua aplicação Universal do Windows. Estes cenários incluem as opções que pode optar por aplicam-se para a aplicação que criou no tutorial [Introdução](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Antes de iniciar este tutorial, primeiro tem de concluir o tutorial de [Introdução](mobile-engagement-windows-store-dotnet-get-started.md) , que é deliberadamente direta e simples. Neste tutorial abrange opções adicionais que pode escolher.

## <a name="specifying-engagement-configuration-at-runtime"></a>Especificação de configuração de Cativação o tempo de execução

A configuração de Cativação é centralizada na `Resources\EngagementConfiguration.xml` ficheiro do projeto, que é onde foi especificado no tópico de [Introdução](mobile-engagement-windows-store-dotnet-get-started.md) .

Mas também pode especificar o tempo de execução: pode ligar para o método seguinte antes da inicialização do agente de Cativação:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Recomendado método: Sobrecarga seu `Page` classes

Para ativar a denúncia de todos os registos necessários por Cativação para calcular os utilizadores, sessões, atividades, falhas e estatísticas técnicas, tornar todos os seus `Page` sub-classes herdam a partir da `EngagementPage` classes.

Eis um exemplo de uma página da sua aplicação. O que pode fazer a mesma coisa para todas as páginas da sua aplicação.

### <a name="c-source-file"></a>Ficheiro de origem c#

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

> [AZURE.IMPORTANT] Se a sua página substitui o `OnNavigatedTo` método, certifique-se de que chamadas `base.OnNavigatedTo(e)`. Caso contrário, não é ser comunicado a atividade (o `EngagementPage` chamadas `StartActivity` dentro da sua `OnNavigatedTo` método).

### <a name="xaml-file"></a>Ficheiro XAML

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

### <a name="override-the-default-behaviour"></a>Substituir o comportamento predefinido

Por predefinição, o nome de classe da página é comunicado como o nome de atividade, com sem extra. Se a classe utiliza o sufixo "Página", Cativação remove-lo.

Para substituir o comportamento predefinido para o nome, adicione este código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Para comunicar informações adicionais com a sua atividade, adicione este código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Estes métodos chamam-se a partir da `OnNavigatedTo` método da sua página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: chamar `StartActivity()` manualmente

Se não conseguir ou não pretende que seja sobrecarga seu `Page` classes, em vez disso, pode começar a suas atividades ao contactar o suporte `EngagementAgent` métodos diretamente.

Recomendamos que estiver a ligar `StartActivity` dentro da sua `OnNavigatedTo` método da sua página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Certifique-se de que termina a sua sessão corretamente.
>
> Liga automaticamente para o Windows SDK Universal a `EndActivity` método quando a aplicação está fechada. Assim, é **vivamente** recomendado para ligar para o `StartActivity` método sempre que a atividade do utilizador alterar e, para **nunca** ligar o `EndActivity` método. Este método notifica o servidor de Cativação que o utilizador actual saiu a aplicação, que irá afetar a todos os registos de aplicação.

## <a name="advanced-reporting"></a>Avançadas de elaboração de relatórios

Opcionalmente, poderá pretender comunicar eventos específicos da aplicação, erros e tarefas, para fazê-lo, utilizam os outros métodos que se encontram na `EngagementAgent` escolares. API Cativação permite a utilização de funcionalidades avançadas de todos os Cativação.

Para obter mais informações, consulte o artigo [como utilizar o Cativação Mobile avançadas etiquetagem API na sua aplicação do Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).
