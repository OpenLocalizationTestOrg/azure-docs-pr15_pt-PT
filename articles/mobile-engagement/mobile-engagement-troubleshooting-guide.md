<properties 
   pageTitle="Guias de resolução de problemas do Azure Cativação móvel" 
   description="Guia de resolução de problemas do Azure Cativação móvel" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Cativação móvel - guia de resolução de problemas

## <a name="introduction"></a>Introdução
O guia de resolução de problemas seguinte irá ajudá-lo a compreender causas raiz de alguns problemas frequentemente viu e irá permitem-lhe resolver problemas no seu próprio. 

## <a name="general"></a>Geral

Em geral, deve sempre garantir o seguinte procedimento:

1. Certifique-se de que foram trocadas através de todos os passos necessários para integração, conforme descrito no nosso [Tutoriais de introdução](mobile-engagement-windows-store-dotnet-get-started.md)
2. Está a utilizar a versão mais recente da plataforma SDK. 
3. Testar um dispositivo real e um emulador uma vez que são específicos emulador apenas alguns problemas. 
4. Não são atingir qualquer limites/limitações de Cativação Mobile que se encontram-se documentados [aqui](../azure-subscription-service-limits.md)
5. Se não conseguir ligar para o serviço do Mobile Cativação back-end ou está a ver dados não a ser carregados continuamente, em seguida, certifique-se de que não existem sem incidentes de serviço em curso, verificando [aqui](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problemas de 'Monitor'

### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Não estou a ver o meu dispositivo aparecem no separador Monitor
Separador Monitor mostra os dispositivos ligados à sua plataforma Mobile Cativação em tempo real. Se estiver a depurar num emulador e dispositivo, deverá ver pelo menos uma sessão aqui. Se a aplicação foi distribuída, em seguida, verá o indicador de sessões activas refletir os dispositivos que estão ligados à plataforma em tempo real. 

Se não estiver a ver o seu dispositivo no separador Monitor, em seguida, é provável que um problema de integração de SDK. Alguns passos para levar para resolver problemas comuns são os seguintes:

1. Certifique-se de que estiver a utilizar a cadeia de ligação correto na aplicação móvel e que é a partir da secção de teclas SDK e não a secção de chaves de API. A cadeia de ligação liga-se a sua aplicação móvel para a instância da aplicação móvel Cativação na qual irá ver o seu dispositivo no separador Monitor. 
2. Para plataforma Windows - se a sua página substitui o `OnNavigatedTo` método, certifique-se para ligar para `base.OnNavigatedTo(e)`.
3. Se integrar o Mobile Cativação para uma aplicação móvel existente, em seguida, também pode garantir que não são falta quaisquer passos verificando os passos de integração avançadas [aqui](mobile-engagement-windows-store-integrate-engagement.md)
4. Certifique-se de que está a enviar pelo menos um ecrã/atividade substituindo a página com EngagementActivity consoante a plataforma que estiver a trabalhar, tal como descrito a [Tutoriais de introdução](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Estou a ver o separador de Monitor a mostrar uma sessão mesmo quando tenho perder a ligação ou fechado a minha aplicação / emulador. 
Se estiver a única pessoa ligada neste momento a plataforma e estiver a utilizar um emulador para abrir a aplicação, isto é muito provavelmente devido ao quirks emulador. Em geral, tem de garantir voltar atrás ao ecrã principal no emulador para a sessão de aplicação desligar com êxito. Para além disso, plataforma do Windows, durante a depuração com o Visual Studio, poderá ter para se certificar de que no Visual Studio, aceda à barra de menus de **Eventos do ciclo de vida** e clique em **Suspender** realmente fechar a sessão. Consulte o artigo [tutorial do Windows](mobile-engagement-windows-store-dotnet-get-started.md) para obter detalhes. 

## <a name="analytics-issues"></a>Problemas de 'Analytics'

### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>Posso não estou a ver todos os dados / atualizados dados no separador de análise 
Dados de análise são recalculados regularmente e pode demorar até 24 horas para esta atualização. Estes dados não se tempo real e irá ver-atualizados dentro este período de tempo de 24 horas.
Certifique-se no entanto que estiver a enviar, pelo menos, um ecrã ou atividade para o back-end plataforma por um dos substituição, pelo menos, uma página com `EngagementActivity` ou chamar `SendActivity` explcitly. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Estou a ver incorretamente capturada data/hora para um dispositivo no separador Analytics
O período de tempo para análise baseia-se a data a partir das definições de dispositivo dos utilizadores. Por isso, certifique-se de que o dispositivo tem a data definida corretamente. 

## <a name="segment-issues"></a>Problemas de 'Segmento'

### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Posso criado um segmento e é apresentada como cinzento ou não a mostrar todos os dados
Criação de segmento não está em tempo real no momento. É calculada ao mesmo tempo como os dados de análise são agregados e para que pode demorar até 24 horas. Deve verificar novamente mais tarde, mas entretanto deverá também Certifique-se de que as aplicações móveis facto vai enviar os dados com base nos quais são formando os segmentos de. Por exemplo Se um evento diga 'foo' não está a ser enviada por qualquer dispositivo móvel, em seguida, seria quaisquer dados segmento para o segmento criado com NomeDoAcontecimento = foo como critério. Também deve verificar a integração do SDK para garantir que a sua aplicação móvel está a enviar corretamente os dados. 

## <a name="reach-or-push-notifications-issues"></a>Problemas de 'Atingir' ou as notificações de emissão

### <a name="my-push-messages-are-not-being-delivered"></a>As minhas mensagens push não estão a ser entregues 

1. Tente enviar notificações para um dispositivo de teste primeiro para garantir que todos os componentes - aplicação móvel, SDK e o serviço estão correctamente ligadas e conseguir entregar notificações push. 
2. Enviar sempre a mais simples 'notificação de fora da aplicação' pela primeira vez através de uma campanha de que não está agendada e nem tiver qualquer critério audiência especificado. Este é novamente para provar a conectividade da notificação está a funcionar corretamente. 
3. Se estiver a ter problemas em fornecer notificações na aplicação, em seguida, também é um bom primeiro passo para tentar enviar uma notificação de fora da aplicação pela primeira vez. 
4. Certifique-se de que o 'nativo emissão' está corretamente configurado para a sua aplicação móvel. Dependendo da plataforma envolvam-lo será quer teclas (Android, Windows) ou certificados (iOS). Consulte o artigo [Interface de utilizador - definições](mobile-engagement-user-interface-settings.md)
5. Terminar aplicação notificações também foi bloqueadas pelo utilizador através do sistema operativo móvel por isso, certifique-se de que não for o caso. 
6. Certifique-se de que esteja não a definir a opção *Ignore audiência, push será enviado para utilizadores através da API* na secção de **campanha** de uma campanha de atingir uma vez que isto irá garantir que as notificações push apenas podem ser enviadas por APIs. 
7. Certifique-se de que estiver a testar campanha push com ambos os um dispositivo ligado através de uma rede de operador de conta e o número de telefone para eliminar a ligação de rede como uma origem de possível de problemas.
8. Certifique-se de que a data/hora do sistema no seu dispositivo/emulador está correta, uma vez qualquer dispositivo está sincronizado também irá interferir com capacidade de notificações Push notificação do serviço para fornecer notificações. 

Plataforma mais instruções abaixo de resolução de problemas específica:

1. **iOS** 

    - Certifique-se de que os certificados são válidas e não expirados para iOS notificações de emissão. 
    - Certifique-se de que está a configurar corretamente um certificado de *produção* na sua aplicação de Cativação Mobile. 
    - Certifique-se de que estiver a testar num *dispositivo real, físico.* O simulator iOS não consegue processar push mensagens.
    - Certifique-se de que o identificador de pacote está corretamente configurado na aplicação móvel. Consulte as instruções [aqui](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
    - Quando testar, utilize a distribuição "Ad Hoc" no seu perfil de aprovisionamento móvel. Não poderá receber uma notificação se a sua aplicação está compilada utilizando "Depurar"

2. **Android**

    - Certifique-se de que especificou o número de projeto correto no ficheiro de AndroidManifest.xml da sua aplicação móvel que é seguido \n caráter. 
    
            <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
        
    - Certifique-se de que não estão em falta ou mal configurado quaisquer permissões no ficheiro manifesto Android 
    - Certifique-se de que o número de projeto que está a adicionar para a sua aplicação de cliente é a partir da mesma conta onde obteve a chave do servidor GCM. Qualquer erro de correspondência entre as duas irá impedir a puxa sejam colocados. 
    - Se estiver a receber as notificações do sistema, mas não na aplicação, em seguida, rever [especificar um ícone para a secção de notificações](mobile-engagement-android-get-started.md) como provável que não está a especificar o ícone correcto no ficheiro manifesto Android. 
    - Se estiver a enviar uma notificação de BigPicture, em seguida, certifique-se de que, se tiver de servidores externos imagem, em seguida, precisam de ser possível suporte HTTP "Obter" e "Chefe".

3. **Windows**
    
    - Certifique-se de que tenha associado a aplicação de uma aplicação da loja Windows válida. No Visual Studio - terá de clique com o botão direito do rato em projeto e selecione a opção "Associar com loja de aplicações" e selecione a aplicação que criou na loja Windows. Esta aplicação da loja Windows deve ser a mesma a partir de onde obteve as credenciais de push nativo para configurar no portal do Cativação Mobile.
    - Se estiver a receber notificações push fora da aplicação, mas não no-notificações da aplicação com `EngagementOverlay` integração, em seguida, certifique-se existe um elemento da grelha de raiz na sua página. EngagementOverlay utiliza o primeiro elemento "Grelha" encontrados no seu ficheiro xaml para adicionar web duas vistas na sua página. Se pretender localizar onde serão definidas vistas da web, pode definir uma grelha com o nome "EngagementGrid" como isto no entanto terá Certifique-se de que existe suficientes altura e largura para a web subsequentes duas vistas que irá mostrar a notificação e os seguintes anúncios como na aplicação notificação:
        
            <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>Que criei um notificação de push/anúncio/de campanha e mesmo depois de ter enviada-me a notificação, é a mostrar como 'Ativa'. O que significa? 
**Campanha** que criou no Mobile Cativação chama-se para porque é um significado de notificações push longa como novos dispositivos ligar à sua plataforma Cativação móvel, estes serão automaticamente enviadas a notificação de que configurar aqui, desde que satisfazem o critério que definir na campanha. Este é não um captura única notificação pelo programa de configuração. Terá de manualmente, clique no botão **Terminar** para terminar a campanha para que não enviar ainda mais notificações. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>Posso criar uma campanha de push e estou a receber notificações com êxito no entanto sempre que abrir a aplicação, obtenho a notificação mesmo mesmo quando posso tinha actioned-lo antes de? 
É provável que deverá acontecer durante o ensaio esta e se estiver a utilizar emuladores ou algumas testar arquitetura como TestFlight. O que se passa Eis que em cada aplicação executar instância, é ao adquirir um novo DeviceID e envie-o para os nossos back-end que está a causar a plataforma de Cativação Mobile tratá-lo como um novo dispositivo e enviar a notificação. 

## <a name="getting-support"></a>Obter suporte

Se não conseguir resolver o problema de si, em seguida, que pode:

1. Procure o problema no threads existentes no fórum de StackOverflow e [Fórum MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) e se não, em seguida, faça uma pergunta aí. 
2. Se encontrar uma funcionalidade em falta, em seguida, adicionar/voto para o pedido no nosso [Fórum de site](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Se tiver Microsoft suporta abrir um incidente de suporte, fornecendo os seguintes detalhes: 
    - ID da subscrição Azure
    - Plataforma (por exemplo, Android etc, iOS)
    - ID da aplicação
    - ID de campanha (para problemas de notificações push)
    - ID do dispositivo
    - Versão Mobile Cativação SDK (por exemplo, v2.1.0 Android SDK)
    - Detalhes do erro com mensagem de erro exata e cenário
