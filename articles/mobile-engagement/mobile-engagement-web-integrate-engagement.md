<properties
    pageTitle="Integração do Azure Mobile Cativação Web SDK | Microsoft Azure"
    description="As atualizações mais recentes e os procedimentos para o Azure Mobile Cativação Web SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="02/29/2016"
    ms.author="piyushjo" />

#<a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrar o Azure Mobile Cativação numa aplicação web

> [AZURE.SELECTOR]
- [Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Os procedimentos neste artigo descrevem a forma mais simples para ativar a monitorização de funções no Azure Mobile Cativação na sua aplicação web e de análise.

Siga os passos para ativar os relatórios de registo que são necessários para calcular todas as estatísticas sobre utilizadores, sessões, atividades, falhas e technicals. Para as estatísticas de dependente da aplicação, tais como eventos, erros e tarefas, tem de ativar relatórios de registo de manualmente ao utilizar a API do Azure Mobile Cativação. Para mais informações, saiba [como utilizar o Cativação Mobile avançadas etiquetagem API numa aplicação web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introdução

[Transferir Web Azure Cativação móvel SDK](http://aka.ms/P7b453).
O Mobile Cativação Web SDK é enviado como um único ficheiro JavaScript, azure-engagement.js, que tem de incluir em cada página do seu site ou aplicação web.

> [AZURE.IMPORTANT] Antes de executar este script, tem de executar um fragmento de código ou script que escrever para configurar o Mobile Cativação para a sua aplicação.

## <a name="browser-compatibility"></a>Compatibilidade de browser

O Mobile Cativação Web SDK utiliza JSON nativo codificar e descodificar, para além de pedidos de AJAX de domínios (depender da especificação de W3C CORS). É compatível com os seguintes browsers:

* Microsoft Edge 12 +
* Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Configurar dispositivos móvel Cativação

Escrever um script que cria uma global `azureEngagement` objeto JavaScript, tal como no exemplo seguinte. Uma vez que o seu site pode ter páginas de múltiplos, este exemplo assume que este script está incluída em todas as páginas. Neste exemplo, se chamar o objeto JavaScript `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

O `connectionString` valor para a aplicação é apresentada no portal do Azure.

> [AZURE.NOTE] `appVersionName`e `appVersionCode` são opcionais. No entanto, recomendamos que configure-los para que a análise de possa processar informações sobre a versão.

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Incluir scripts Mobile Cativação nas suas páginas
Adicione scripts Mobile Cativação às suas páginas de uma das seguintes formas:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Ou este:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias

Depois do script Mobile Cativação Web SDK é carregado, que cria o alias de **Cativação** para aceder à API do SDK. Não pode utilizar este alias para definir a configuração de SDK. Este alias é utilizado como uma referência nesta documentação.

Tenha em atenção que se o alias predefinido entra em conflito com outro variável global a partir da sua página, pode redefini-la na configuração da seguinte forma antes de carregar o Mobile Cativação Web SDK:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Relatório básico

Relatório básico no Mobile Cativação abrange as estatísticas de nível de sessão, tal como estatísticas sobre utilizadores, sessões, atividades e causa uma falha.

### <a name="session-tracking"></a>Controlo de sessão

Uma sessão de Cativação Mobile estiver dividida numa sequência de atividades, cada uma identificada por um nome.

Num Web site clássico, recomendamos que declarar uma actividade diferente em cada página do seu site. Para uma Web site ou aplicação web na qual a página atual nunca é alterado, poderá pretender controlar as atividades numa escala mais pequena, tais como dentro de uma página.

Qualquer forma, para começar a ou alterar a atividade de utilizador atual, contacte o `engagement.agent.startActivity` função. Por exemplo:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

O servidor de Cativação Mobile termina automaticamente uma sessão de abrir dentro de três minutos depois da página de aplicações estiver fechada.

Em alternativa, pode terminar uma sessão manualmente ao contactar o suporte `engagement.agent.endActivity`. Este procedimento define a atividade de utilizador atual para 'Inactivo'.  A sessão terminará 10 segundos mais tarde, a menos que uma nova chamada para `engagement.agent.startActivity` currículos a sessão.

Pode configurar o atraso de 10 segundo no objeto Cativação global, da seguinte forma:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] Não é possível utilizar `engagement.agent.endActivity` na `onunload` chamada de retorno porque não pode efetuar chamadas de AJAX nesta fase.

## <a name="advanced-reporting"></a>Avançadas de elaboração de relatórios

Opcionalmente, se pretender comunicar eventos específicos da aplicação, erros e tarefas, terá de utilizar a API de Cativação Mobile. Aceder a API Cativação Mobile através de `engagement.agent` objeto.

Pode aceder a todas as funcionalidades avançadas no Mobile Cativação na API Cativação Mobile. A API é detalhada no artigo [como utilizar o Cativação Mobile avançadas etiquetagem API numa aplicação web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Personalizar os URLs utilizados para chamadas de AJAX

Pode personalizar URLs que utiliza o Mobile Cativação Web SDK. Por exemplo, para redefinir o URL de registo (o SDK ponto final para o registo), pode substituir a configuração da seguinte forma:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Se o seu funções de URL devolvem uma cadeia que começa com `/`, `//`, `http://`, ou `https://`, não é utilizado o esquema predefinido. Por predefinição, o `https://` esquema é utilizado para esses URLs. Se quiser personalizar o esquema predefinido, substitui a configuração, da seguinte forma:

    window.azureEngagement = {
      ...
      urls: {
        ...      
        scheme: '//'
      }
    };
