<properties
    pageTitle="Introdução ao Azure Mobile Cativação para Windows Phone Silverlight apps"
    description="Saiba como utilizar o Azure Mobile Cativação com as notificações push e de análise para as aplicações do Windows Phone Silverlight."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Introdução ao Azure Mobile Cativação para Windows Phone Silverlight apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Cativação para compreender a utilização da aplicação e enviar as notificações push para segmentado utilizadores de uma aplicação do Windows Phone Silverlight.
Neste tutorial demonstra o cenário de difusão simple utilizando Cativação Mobile. -Lo, pode criar uma aplicação do Windows Phone Silverlight em branco que recolhe dados básico e recebe as notificações push utilizando o serviço de notificações Push (MPNS) da Microsoft.

> [AZURE.NOTE] Se está a filtrar o Windows Phone 8.1 (que não sejam Silverlight), consulte o [tutorial Universal do Windows](mobile-engagement-windows-store-dotnet-get-started.md).

Neste tutorial requer o seguinte:

+ Visual Studio 2013
+ [MicrosoftAzure.MobileEngagement] Nuget pacote

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Configuração móvel Cativação para o seu Windows Phone aplicação

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Ligar a sua aplicação ao Mobile Cativação back-end

Neste tutorial apresenta uma "básica integração", que é o conjunto mínimo necessário para recolher dados e enviar uma notificação de emissão. A documentação de integração completa pode ser encontrada na [integração com o Mobile Cativação Windows Phone SDK](mobile-engagement-windows-phone-sdk-overview.md)

Vamos irá criar uma aplicação básica com o Visual Studio para demonstrar a integração.

###<a name="create-a-new-windows-phone-silverlight-project"></a>Criar um novo projeto do Windows Phone Silverlight

Os seguintes passos partem do pressuposto a utilização do Visual Studio 2015 apesar dos passos são semelhantes em versões anteriores do Visual Studio. 

1. Inicie o Visual Studio e, no ecrã **principal** , selecione **Novo projeto**.

2. No pop-up, selecione o **Windows 8** -> **Windows Phone** -> **Aplicação vazia (Windows Phone Silverlight)**. Preencha a aplicação **nome** e o **nome de solução**e, em seguida, clique em **OK**.

    ![][1]

3. Pode optar por desenvolva o **Windows Phone 8.0** ou **Windows Phone 8.1**.

Agora ter criado uma nova aplicação do Windows Phone Silverlight nas quais podemos integrar o Azure Mobile Cativação SDK.

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Ligar a sua aplicação ao Mobile Cativação back-end

1. Instale o pacote de nuget [MicrosoftAzure.MobileEngagement] no projeto.

2. Abrir `WMAppManifest.xml` (em pasta propriedades) e certifique-se da seguinte é declarada (adicioná-los se não estiverem) na `<Capabilities />` etiqueta:

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Agora colar a cadeia de ligação que copiou anteriormente para a sua aplicação Mobile Cativação e colá-lo na `Resources\EngagementConfiguration.xml` de ficheiros, entre o `<connectionString>` e `</connectionString>` etiquetas:

    ![][3]

4. No `App.xaml.cs` ficheiro:

    um. Adicionar o `using` declaração:

            using Microsoft.Azure.Engagement;

    b. Iniciar o SDK na `Application_Launching` método:

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. Inserir o seguinte procedimento na `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>Ativar a monitorização em tempo real

Para poder começar a enviar dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã (actividade) para o back-end Cativação Mobile.

1. Na MainPage.xaml.cs, adicione o `using` declaração:

        using Microsoft.Azure.Engagement;

2. Substitua a classe de base de **MainPage**, que é antes de **PhoneApplicationPage**, **EngagementPage**.

        class MainPage : EngagementPage 
    
3. No seu `MainPage.xml` ficheiro:

    um. Adicione a sua declarações de espaços de nomes:

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. Substituir `phone:PhoneApplicationPage` no nome de etiqueta XML com `engagement:EngagementPage`.

##<a id="monitor"></a>Ligar a aplicação com monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar as notificações push e mensagens de na aplicação

Cativação Mobile permite-lhe interagir e alcançar os utilizadores com as notificações de emissão e mensagens de na aplicação no contexto de campanhas. Este módulo chama alcance no portal do Cativação Mobile.
As secções seguintes configurar a sua aplicação para recebê-las.

###<a name="enable-your-app-to-receive-mpns-push-notifications"></a>Ativar a sua aplicação receber notificações de emissão MPNS

Adicionar novas capacidades para sua `WMAppManifest.xml` ficheiro:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###<a name="initialize-the-reach-sdk"></a>Iniciar o SDK alcance

1. No `App.xaml.cs`, chamada `EngagementReach.Instance.Init();` na função **Application_Launching** imediatamente após a inicialização agente:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. No `App.xaml.cs`, chamada `EngagementReach.Instance.OnActivated(e);` na função **Application_Activated** imediatamente após a inicialização agente:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Está pronto. Agora podemos vai verificar que corretamente ter disse saída esta integração básica.

##<a id="send"></a>Enviar uma notificação para a sua aplicação

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Agora deverá ver uma notificação no seu dispositivo que irá aparecer a como uma notificação de na aplicação, se a aplicação estiver aberta caso contrário, como uma notificação de alerta de como o seguinte: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
