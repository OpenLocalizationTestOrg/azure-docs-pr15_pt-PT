<properties 
    pageTitle="Do Windows Phone Silverlight SDK procedimentos de actualização" 
    description="Do Windows Phone Silverlight SDK procedimentos de actualização para Azure Cativação móvel"                  
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

#<a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Do Windows Phone Silverlight SDK procedimentos de actualização

Se já tiver o integrado uma versão anterior do nosso SDK para a aplicação, tem de ter em conta os seguintes pontos ao atualizar o SDK.

Poderá ter de seguir vários procedimentos se perdeu várias versões do SDK. Por exemplo, se migrar a partir do 0.10.1 para 0.11.0 que tem de pela primeira vez, siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

##<a name="from-200-to-330"></a>A partir do 2.0.0 para 3.3.0

### <a name="test-logs"></a>Registos de teste

Registos de consola produzidos pelo SDK agora podem ser ativado/desactivado/filtrados. Para personalizar isto, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um do valor disponível a partir de `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

##<a name="from-111-to-200"></a>A partir do 1.1.1 para 2.0.0

A seguinte tabela descreve como migrar uma integração SDK de serviço Capptain oferecido pelo Capptain SA para uma aplicação tecnologia do Azure Mobile Cativação. 

> [Azure.IMPORTANT] Capptain e Mobile Cativação não são os mesmos serviços e o procedimento indicado abaixo realça apenas como migrar a aplicação de cliente. Migrar o SDK na aplicação irá não migrar os dados a partir os servidores de Capptain para os servidores de Cativação Mobile

Se estiver a migrar de uma versão anterior, consulte o web site da Capptain migrar para o 1.1.1 pela primeira vez, em seguida, aplique o procedimento seguinte

### <a name="nuget-package"></a>Pacote Nuget

Substitua **Capptain.WindowsPhone** ao **MicrosoftAzure.MobileEngagement** Nuget pacote.

### <a name="applying-mobile-engagement"></a>Aplicar Cativação móvel

O SDK utiliza o termo `Engagement`. Tem de atualizar o seu projeto para que correspondam a esta alteração.

Tem de desinstalar o seu pacote de nuget Capptain atual. Considere esse serão removidas todas as suas alterações na pasta Capptain recursos. Se pretender manter os ficheiros em seguida, faça uma cópia dos mesmos.

Após esta ação, instale o pacote de nuget de Cativação do Microsoft Azure novo no projeto. Pode encontrá-lo diretamente no [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Esta ação substitui todos os ficheiros de recursos utilizados pelo Cativação e adiciona a nova DLL de Cativação para as referências do projecto.

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

4. Para os outros recursos semelhante a imagens Capptain, tenha em atenção que estes também nomes foram mudados para utilizar "Cativação".

### <a name="application-id--sdk-key"></a>ID da aplicação / SDK chave

Cativação utiliza uma cadeia de ligação. Não tem de especificar um ID da aplicação e uma tecla SDK com Cativação Mobile, só tem de especificar uma cadeia de ligação. Pode configurar-o no seu ficheiro EngagementConfiguration.

A configuração de Cativação pode ser definida no seu `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar:

-   A cadeia de ligação de aplicação entre etiquetas `<connectionString>` e `<\connectionString>`.

Se pretender especificá-la o tempo de execução, pode ligar o método seguinte antes da inicialização do agente de Cativação:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

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



 
