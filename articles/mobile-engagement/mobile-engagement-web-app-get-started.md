<properties
    pageTitle="Introdução ao Azure Mobile Cativação para Web Apps | Microsoft Azure"
    description="Saiba como utilizar o Azure Mobile Cativação com as notificações push e de análise para Web Apps."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="js"
    ms.topic="hero-article"
    ms.date="06/01/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Introdução ao Azure Mobile Cativação para Web Apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Cativação para compreender a utilização do Web App.

Neste tutorial requer o seguinte:

+ Visual Studio 2015 ou qualquer outro editor da sua preferência
+ [Web SDK](http://aka.ms/P7b453) 

Este SDK Web é na pré-visualização e apenas suporta a análise no momento e não suporta envio browser ou na aplicação as notificações push ainda. 

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##<a name="setup-mobile-engagement-for-your-web-app"></a>Configuração móvel Cativação para a sua aplicação Web

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Ligar a sua aplicação ao Mobile Cativação back-end

Neste tutorial apresenta uma "básica integração," qual é o conjunto mínimo necessário para recolher dados.

Vamos irá criar uma aplicação web básica com o Visual Studio para demonstrar a integração apesar pode seguir os passos com uma aplicação web também criada fora do Visual Studio. 

###<a name="create-a-new-web-app"></a>Criar uma nova aplicação Web

Os seguintes passos partem do pressuposto a utilização do Visual Studio 2015 apesar dos passos são semelhantes em versões anteriores do Visual Studio. 

1. Inicie o Visual Studio e, no ecrã **principal** , selecione **Novo projeto**.

2. No pop-up, selecione **Web** -> **Aplicação Web do ASP.Net**. Preencha a aplicação **nome**, **localização** e o **nome da solução**e, em seguida, clique em **OK**.

3. No menu de contexto **Selecione um modelo** , selecione **vazio** em **ASP.Net 4,5 modelos** e clique em **OK**. 

Agora ter criado um novo projeto de Web App em branco no qual podemos integrar o Azure Mobile Cativação Web SDK.

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a sua aplicação ao back-end de Cativação Mobile

1. Crie uma nova pasta denominada **javascript** na sua solução e adicione o ficheiro de Web SDK JS **azure engagement.js** na mesma. 

2. Adicione um novo ficheiro denominado **main.js** nesta pasta javascript com o código seguinte. Certifique-se atualizar a cadeia de ligação. Isto `azureEngagement` objeto será utilizado para aceder a métodos de Web SDK. 

        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };

    ![Visual Studio com os ficheiros de js][1]

##<a name="enable-real-time-monitoring"></a>Ativar a monitorização em tempo real

Para poder começar a enviar dados e garantir que os utilizadores estão ativos, terá de enviar pelo menos uma atividade Mobile Cativação back-end. Uma actividade no contexto de uma aplicação web é uma página web. 

1. Criar uma nova página denominada **home.html** na sua solução e defini-lo como a página de início para a sua aplicação web. 
2. Inclua os duas javascripts que adicionámos anteriormente nesta página, adicionando o seguinte dentro da tag do corpo. 

        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Atualizar a etiqueta de corpo para ligar do EngagementAgent `startActivity` método
        
        <body onload="engagement.agent.startActivity('Home')">

4. Eis o aspeto seu **home.html**
        
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

##<a name="connect-app-with-real-time-monitoring"></a>Ligar a aplicação com monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##<a name="extend-analytics"></a>Expandir analytics

Aqui estão atualmente disponíveis com Web SDK, que pode utilizar para a análise de todos os métodos:

1. Páginas de atividades/Web:

        engagement.agent.startActivity(name);
        engagement.agent.endActivity();

2. Eventos
        
        engagement.agent.sendEvent(name, extras);

3. Erros

        engagement.agent.sendError(name, extras);

4. Tarefas

        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

