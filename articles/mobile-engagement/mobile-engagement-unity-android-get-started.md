<properties
    pageTitle="Introdução ao Azure Mobile Cativação para implementação unidade Android"
    description="Saiba como utilizar Azure Mobile Cativação com as notificações de emissão e de análise unidade aplicações para implementar para dispositivos iOS."
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>Introdução ao Azure Mobile Cativação para implementação unidade Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Cativação para compreender a utilização da aplicação e como enviar notificações push para segmentado, os utilizadores de uma aplicação de unidade quando implementar o dispositivo Android.
Neste tutorial utiliza a unidade clássica filmar um tutorial bola como ponto de partida. Deve seguir os passos neste [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de prosseguir com a integração de Cativação Mobile que podemos exibir os no tutorial abaixo. 

Neste tutorial requer o seguinte:

+ [Editor de unidade](http://unity3d.com/get-unity)
+ [Unidade de Cativação móvel SDK](https://aka.ms/azmeunitysdk)
+ Google Android SDK

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).

##<a id="setup-azme"></a>Configuração móvel Cativação para a sua aplicação Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Ligar a sua aplicação ao Mobile Cativação back-end

###<a name="import-the-unity-package"></a>Importar o pacote de unidade

1. Transferir o [pacote de unidade de Cativação Mobile](https://aka.ms/azmeunitysdk) e guardá-lo no seu computador local. 

2. Aceda a **activos -> pacote importar -> pacote personalizada** e selecione o pacote que transferiu no passo acima. 

    ![][70] 

3. Certifique-se de todos os ficheiros são selecionados e clique em botão **Importar** . 

    ![][71] 

4. Depois de importar for bem sucedido, irá ver os ficheiros SDK importados no seu projeto.  

    ![][72] 

###<a name="update-the-engagementconfiguration"></a>Atualizar a EngagementConfiguration

1. Abrir o ficheiro de script de **EngagementConfiguration** a partir da pasta SDK e atualizar o **ANDROID\_ligação\_cadeia** com a cadeia de ligação que anteriormente obtidos a partir do portal Azure.  

    ![][73]

2. Guardar o ficheiro 

3. Executar **ficheiro -> Cativação -> gerar manifesto Android**. Este é o plug-in adicionado pelo SDK Cativação Mobile e clicar no mesmo irá atualizar automaticamente as suas definições de projeto. 

    ![][74]

> [AZURE.IMPORTANT] Certifique-se executar esta sempre atualiza o ficheiro de **EngagementConfiguration** contrário que as suas alterações só serão refletidas na aplicação. 

###<a name="configure-the-app-for-basic-tracking"></a>Configurar a aplicação para controlo básico

1. Abrir configurar o script **PlayerController** anexado ao objeto Player para edição. 

2. Adicione o seguinte utilizando instrução:

        using Microsoft.Azure.Engagement.Unity;

3. Adicionar o seguinte procedimento para o `Start()` método
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###<a name="deploy-and-run-the-app"></a>Implementar e executar a aplicação
Certifique-se de que tem SDK Android instalado no seu computador antes de tentar implementar esta aplicação de unidade para o seu dispositivo. 

1. Ligar o dispositivo Android para o seu computador. 

2. Abrir o **ficheiro -> criar definições** 

    ![][40]

3. Selecione **Android** e, em seguida, clique em **Mudar** plataforma

    ![][51]

    ![][52]

4. Clique em **definições do leitor** e forneça um identificador de pacote válido. 

    ![][53]

5. Por fim, clique em **Criar e executar**

    ![][54]

6. Poderá ser-lhe pedido para fornecer um nome de pasta para armazenar o pacote Android. 

7. Se tudo o que vai fino, em seguida, o pacote será implementado no seu dispositivo ligado e deverá ver o seu jogo unidade no seu telemóvel! 

##<a id="monitor"></a>Ligar a aplicação com monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar as notificações push e mensagens de na aplicação

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###<a name="update-the-engagementconfiguration"></a>Atualizar a EngagementConfiguration

1. Abrir o ficheiro de script de **EngagementConfiguration** a partir da pasta SDK e atualizar o **ANDROID\_GOOGLE\_número** com o **Número de projeto do Google** que anteriormente obtidos a partir do portal do Google Cloud programador. Esta é uma cadeia de valor, por isso, certifique-se de incluir-aspas. 

    ![][75]

2. Guarde o ficheiro. 

3. Executar **ficheiro -> Cativação -> gerar manifesto Android**. Este é o plug-in adicionado pelo SDK Cativação Mobile e clicar no mesmo irá atualizar automaticamente as suas definições de projeto. 

    ![][74]

###<a name="configure-the-app-to-receive-notifications"></a>Configurar a aplicação para receber notificações

1. Abrir configurar o script **PlayerController** anexado ao objeto Player para edição. 

2. Adicionar o seguinte procedimento para o `Start()` método

        EngagementReachAgent.Initialize();

3. Agora que a aplicação é atualizada, implementar e executar a aplicação num dispositivo de acordo com as instruções fornecidas abaixo. 

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png
