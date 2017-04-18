<properties
    pageTitle="Introdução ao Azure Mobile Cativação para implementação do iOS de unidade"
    description="Saiba como utilizar Azure Mobile Cativação com as notificações de emissão e de análise unidade aplicações para implementar para dispositivos iOS."
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Introdução ao Azure Mobile Cativação para implementação do iOS de unidade

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Cativação para compreender a utilização da aplicação e como enviar notificações push para segmentado, os utilizadores de uma aplicação de unidade quando implementar o dispositivo iOS.
Neste tutorial utiliza a unidade clássica filmar um tutorial bola como ponto de partida. Deve seguir os passos neste [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de prosseguir com a integração de Cativação Mobile que podemos exibir os no tutorial abaixo. 

Neste tutorial requer o seguinte:

+ [Editor de unidade](http://unity3d.com/get-unity)
+ [Unidade de Cativação móvel SDK](https://aka.ms/azmeunitysdk)
+ Editor de XCode

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).

##<a id="setup-azme"></a>Configuração móvel Cativação para a sua aplicação iOS

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

1. Abrir o ficheiro de script de **EngagementConfiguration** a partir da pasta SDK e atualizar o **IOS\_ligação\_cadeia** com a cadeia de ligação que anteriormente obtidos a partir do portal Azure.  

    ![][73]

2. Guarde o ficheiro. 

###<a name="configure-the-app-for-basic-tracking"></a>Configurar a aplicação para controlo básico

1. Abrir configurar o script **PlayerController** anexado ao objeto Player para edição. 

2. Adicione o seguinte utilizando instrução:

        using Microsoft.Azure.Engagement.Unity;

3. Adicionar o seguinte procedimento para o `Start()` método
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###<a name="deploy-and-run-the-app"></a>Implementar e executar a aplicação

1. Ligar o dispositivo iOS para o seu computador. 

2. Abrir o **ficheiro -> criar definições** 

    ![][40]

3. Selecione **iOS** e, em seguida, clique em **Mudar** plataforma

    ![][41]

    ![][42]

4. Clique em **definições do leitor** e forneça um identificador de pacote válido. 

    ![][53]

5. Por fim, clique em **Criar e executar**

    ![][54]

6. Poderá ser-lhe pedido para fornecer um nome de pasta para armazenar o pacote do iOS. 

    ![][43]

7. Se tudo o que vai fino, em seguida, o projeto deve ser compilado e deve abrir para cima na sua aplicação XCode. 

8. Certifique-se de que a **juntar identificador** está correto no projeto.  

    ![][75]

10. Agora, execute a aplicação no XCode para que o pacote é implementado para o seu dispositivo ligado e deverá ver o seu jogo unidade no seu telemóvel! 

##<a id="monitor"></a>Ligar a aplicação com monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar as notificações push e mensagens de na aplicação

Cativação Mobile permite-lhe interagir com os seus utilizadores e ALCANÇAR com as notificações push e mensagens no contexto das campanhas de na aplicação. Este módulo chama alcance no portal do Cativação Mobile.
Não tem de efetuar qualquer configuração adicional na sua aplicação para receber notificações e já está configurada para a mesma.

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
