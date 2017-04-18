<properties 
    pageTitle="Notificação Azure concentradores - diretrizes de diagnóstico" 
    description="Diretrizes sobre como diagnosticar problemas comuns com concentradores de notificação do Azure." 
    services="notification-hubs" 
    documentationCenter="Mobile" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs---diagnosis-guidelines"></a>Azure notificação concentradores - diretrizes de diagnóstico

##<a name="overview"></a>Descrição geral

Uma das perguntas mais comuns podemos opinião clientes Azure notificação concentradores é como descobrir por que motivo não verão uma notificação enviada a partir da sua aplicação do back-end aparecem no dispositivo cliente - onde e por que motivo notificações foram largadas e como corrigir este problema. Neste artigo iremos várias razões porque é que o notificações poderão obter capituladas ou não terminem nos dispositivos. Vamos também através de formas nos quais pode analisar e descobrir a causa de raiz. 

Em primeiro lugar, é fundamental para compreender como Azure notificação concentradores emite notificações para os dispositivos.
![][0]

Num fluxo típica Enviar notificação, a mensagem é enviada a partir de **back-end de aplicação** para **Concentrador de notificação do Azure (NH)** , que por sua vez não algum processamento em todos os registos tendo em conta as etiquetas configuradas e expressões de etiqueta para determinar "destinos", ou seja, todos os registos que precisam de receber a notificação de push. Podem expandir estes registos através de alguns ou todos os nossos plataformas suportadas - iOS, Google, Windows, Windows Phone, Kindle e Baidu para Android na China. Depois dos destinos são estabelecidos, NH, em seguida, puxa notificações, dividir através de vários lote de registos, para a plataforma do dispositivo específico **Serviço de notificações de emissão (PNS)** - por exemplo, APNS para Apple, GCM para Google etc. NH autentica com as respectivas PNS com base nas credenciais que definir no Portal clássica do Azure na página Configurar concentrador de notificação. O PNS reencaminha, em seguida, as notificações para os respetivos **dispositivos do cliente**. Esta é a plataforma recomendada forma de entregar notificações push e tenha em atenção que a ramificação final de entrega de notificação ocorre o mais entre a plataforma PNS e o dispositivo. Por conseguinte, temos quatro componentes principais - *cliente*, *back-end de aplicação*, *Azure notificação concentradores (NH)* e *Serviços de notificações de emissão (PNS)* e qualquer um destes possa provocar notificações interrompidas. Obter mais detalhes sobre esta arquitetura está disponível em [Descrição geral de concentradores de notificação].

Se não conseguir entregar notificações poderá ocorrer durante o ensaio/teste inicial fase que podem indicar um problema de configuração ou pode acontecer onde todas ou algumas das notificações de podem ser obter ignorados indicando algumas mais aprofundada aplicações ou mensagens problema padrão de produção. Na secção, abaixo vamos abordar vários notificações ignorados cenários que se situa entre comuns do tipo raros, que algumas das quais poderá constatar óbvios e algumas outras pessoas não muito. 

##<a name="azure-notifications-hub-mis-configuration"></a>Azure concentrador de notificações erros de configuração 

Necessita de Azure concentradores de notificação para se autenticar no contexto da aplicação o programador possam enviar com êxito notificações para os respetivos PNS. Isto é tornado possível pelo programador criar uma conta de programador com a plataforma respetivas (Google, Apple, Windows etc) e, em seguida, registar a sua aplicação onde obterem estas credenciais que precisam de ser configurado no portal na secção de configuração de notificação concentradores. Se estiver a fazer através de sem notificações, primeiro passo deverá ser para se certificar de que as credenciais corretas estão configuradas no centro do notificação correspondência-los com a aplicação criada em respetiva conta do programador específico de plataforma. Irá encontrar os nossos [Introdução tutoriais iniciado] útil para abordar este processo de uma forma de passo a passo. Aqui estão algumas configurações erros comuns:

1. **Geral**
 
    um) certifique-se de que o seu nome de concentrador de notificação (sem erros de digitação) é o mesmo:

    - Onde está a registar a partir do cliente, 
    - Onde está a enviar notificações a partir de back-end,  
    - Sempre que tiver configurado as credenciais PNS e 
    - Cujas credenciais SA tiver configurado no cliente e o back-end. 
        
    b) certifique-se de que está a utilizar as cadeias de configuração de SA corretas no cliente e o back-end de aplicação. Como uma regra útil, tem de utilizar o **DefaultListenSharedAccessSignature** no cliente e **DefaultFullSharedAccessSignature** no back-end de aplicação (o que concede permissão para poder enviar notificação para o NH)

2. **Configuração do serviço de notificações de emissão ' (APNS) da Apple**
 
    Deve manter duas concentradores - um para produção e outro para testar a ligação para fins. Isto significa que carregar o certificado que vai utilizar sandbox ambiente a um concentrador separado e o certificado que vai utilizar no produção a um concentrador em separado. Não tente carregar diferentes tipos de certificados para o centro do mesmo como poderá causar falhas de notificação de linha para baixo. Se encontrar-se numa posição onde inadvertidamente carregou diferentes tipos de certificado para o mesmo concentrador, é recomendável para eliminar o concentrador e começar a trabalhar. Se, por alguma razão, não for possível eliminar o concentrador, em seguida, no mínimo, tem de eliminar todos os registos existentes do concentrador. 

3. **Configuração do Google Cloud mensagens (GCM)** 

    um) certifique-se de que está a activar "Google Cloud mensagens para Android" em projeto na nuvem. 
    
    ![][2]
    
    b) certifique-se de que cria uma chave"servidor" enquanto obter as credenciais que NH irá utilizar para autenticar com GCM. 
    
    ![][3]
    
    c) certifique-se de que configurou "ID do projecto" no cliente que é uma entidade totalmente numérica que pode obter a partir do dashboard:
    
    ![][1]

##<a name="application-issues"></a>Problemas de aplicação

1) **Etiquetas / marcar expressões**

Se estiver a utilizar etiquetas ou expressões de etiqueta para segmentar a sua audiência, sempre é possível que quando estiver a enviar notificação, não existe nenhuma destino foi encontrado com base nas expressões de etiquetas/etiqueta que está a especificar na sua chamada enviar. É melhor rever os registos para se certificar de que não existem etiquetas que correspondem ao enviar uma notificação e, em seguida, verifique se o recibo de notificação de apenas a partir de clientes com esses registos. Por exemplo Se todos os seus registos com NH estar concluídos com diga etiqueta "Políticas de alto nível" e estiver a enviar uma notificação com etiqueta "Desportos", não será enviada para qualquer dispositivo. Um caso complexo poderia envolvam expressões de etiqueta onde que só registado com "A etiqueta" ou "B etiqueta" mas durante o envio de notificações, está a filtrar "Etiqueta A & & etiqueta B". Na secção personalizada diagnosticar sugestões abaixo, existem formas na qual pode rever os seus registos juntamente com as etiquetas que tenham. 

2) **Problemas de modelo**

Se estiver a utilizar modelos, certifique-se de que está a seguir as orientações descritas na [orientação de modelo]. 

3) **Registos inválidos**

Assumindo que o concentrador de notificação foi corretamente configurado e as expressões de etiquetas/etiqueta foram utilizadas corretamente que resulta em localizar de destinos válidos para que as notificações tem de ser enviada, NH é acionada desativar várias secções de processamento em paralelo - cada lote de envio de mensagens para um conjunto de registos. 

> [AZURE.NOTE] Uma vez que recomendamos fazer o processamento em paralelo, podemos não garante a ordem pela qual as notificações serão enviadas. 

Agora concentrador de notificações do Azure está optimizada para um modelo de entrega de mensagem "na mais uma vez". Isto significa que vamos tentar uma a duplicação para que não notificações são entregues mais do que uma vez para um dispositivo. Para garantir que este percorrer os registos e certifique-se de que apenas uma mensagem é enviada por identificador de dispositivo de antes de enviar realmente a mensagem para a PNS. À medida que cada lote é enviada para PNS, que por sua vez é a aceitar e validar os registos, é possível que o PNS Deteta um erro com um ou mais registos num lote devolve um erro ao Azure NH e deixa de processamento portanto largando desse lote completamente. Isto é especialmente verdade com APNS que utiliza um protocolo da cadeia TCP. Apesar do podemos estão otimizados para na maioria depois de entrega, neste caso, podemos remover o registo em falta da nossa base de dados e, em seguida, volte a tentar entrega de notificação para o resto dos dispositivos que está desse lote.

Pode obter informações de erro para a tentativa de entrega falhou relativamente a um registo utilizando o Azure notificação concentradores REST APIs: [por mensagem de telemetria: obter telemetria de mensagem de notificação](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [PNS comentários](https://msdn.microsoft.com/library/azure/mt705560.aspx). Consulte o artigo [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) por exemplo, código.

##<a name="pns-issues"></a>Problemas PNS

Assim que a mensagem de notificação foi recebida pelos respetivos PNS é sua responsabilidade a notificação para o dispositivo. Azure notificação concentradores estiver fora do aqui na imagem e não tem controlo quando ou se a notificação vai ser entregue no dispositivo. Uma vez que os serviços de notificação de plataforma são muito robustos, notificações tendência para atingir os dispositivos de aguardar alguns segundos a partir do PNS. Se o PNS é limitação no entanto, em seguida, Azure notificação concentradores aplique uma anterior exponencial desativar estratégia e se o PNS permanece inacessível para 30 mínimo, em seguida, temos uma política no local para expirar e largue as mensagens de forma permanente. 

Se um PNS tenta entregar uma notificação, mas o dispositivo está offline, a notificação é armazenada à PNS durante um período de tempo limitado e entregue ao dispositivo quando fica disponível. Apenas uma notificação recente para uma aplicação em particular está armazenada. Se forem enviadas várias notificações enquanto o dispositivo está offline, cada notificação de novo faz com que a notificação prévia ser eliminados. Este comportamento de manter apenas a mais recente notificação é referido como misturam notificações no APNS e fechando no GCM (que utiliza uma chave de fecho). Se o dispositivo permanece offline muito tempo, todas as notificações que foram a ser guardadas para a mesma são eliminadas. Origem - [orientações APNS] & [orientações GCM]

Com o Azure notificação concentradores - pode ser efetuada com uma chave de Coalescer através de um cabeçalho de HTTP utilizando o genérico `SendNotification` API (por exemplo, para .NET SDK – `SendNotificationAsync`) que também leva-o até cabeçalhos de HTTP são transmitidos como é os respetivos PNS. 

##<a name="self-diagnose-tips"></a>Diagnosticar personalizada sugestões

Aqui iremos examinar os vários hipóteses para diagnosticar e raiz causam problemas concentrador de notificação:

###<a name="verify-credentials"></a>Verificar as credenciais

1. **Developer portal para PNS**

    Verifique-los no respetivos PNS developer portal (WNS, APNS, GCM, etc) utilizando o nosso [Introdução tutoriais iniciado].

2. **Portal do Azure clássico**

    Aceda ao separador Configurar para rever e corresponder as credenciais com os obtidos a partir do portal de programador PNS. 

    ![][4]

###<a name="verify-registrations"></a>Verificar registos

1. **Visual Studio**

    Se utilizar o Visual Studio para o desenvolvimento, em seguida, pode ligar ao Microsoft Azure e ver e gerir um conjunto de serviços Azure, incluindo concentrador de notificações a partir do "Explorador de servidor". Isto é sobretudo útil para o seu ambiente de teste/Dev Center. 

    ![][9]

    Pode ver e gerir todos os registos no seu centro que são categorizados corretamente para plataforma, nativa ou registo de modelo, quaisquer etiquetas, PNS identificador, id de registo e a data de expiração. Também pode editar um registo no momento - que é útil apresentamos se pretende editar quaisquer etiquetas. 

    ![][8]
 
    > [AZURE.NOTE] Visual Studio funcionalidade para editar os registos só deve ser utilizada durante Dev Center/teste com um número limitado de registos. Se surgir existir uma necessidade de corrigir os seus registos em volume, considere a utilização da funcionalidade de registo de exportação/importação descritas aqui - [Exportação/importação registos] (https://msdn.microsoft.com/library/dn790624.aspx)

2. **Explorer Bus de serviço**

    Muitos clientes utilizam ServiceBus explorer descritas aqui - [ServiceBus Explorer] para ver e gerir os seu concentrador de notificação. É um projeto de abrir origem disponível a partir do code.microsoft.com - [ServiceBus Explorer código]

###<a name="verify-message-notifications"></a>Certifique-se de notificações de mensagens

1. **Azure Portal clássico**

    Pode aceder ao separador "Depuração" enviar notificações de teste para os seus clientes sem necessitar de qualquer back-end do serviço para cima e a ser executado. 

    ![][7]

2. **Visual Studio**

    Também pode enviar notificações de teste do comforts do Visual Studio:

    ![][10]

    Pode ler mais sobre a funcionalidade de explorer Azure de concentrador de notificação do Visual Studio aqui - 
    
    - [Descrição geral do Explorador de servidor VS]
    - [Vs. no servidor Explorer mensagem no blogue - 1]
    - [Vs. no servidor Explorer mensagem no blogue - 2]

###<a name="debug-failed-notifications-review-notification-outcome"></a>Notificações de falhadas de depuração / rever resultado de notificação

**Propriedade EnableTestSend**

Quando envia uma notificação através de notificação concentradores, inicialmente-la apenas obtém na fila para NH fazer processamento descobrir todos os seus alvos e, em seguida, eventualmente NH envia-o para a PNS. Isto significa que quando estiver a utilizar a REST API ou de qualquer página do cliente SDK, o retorno bem sucedido da sua chamada enviar apenas significa que a mensagem foi com êxito colocado fila para cima com concentrador de notificação. Que não lhe dá uma visão o que aconteceu ao NH eventualmente tem enviar a mensagem para PNS. Se a sua notificação de não está a chegar ao dispositivo cliente, existe a possibilidade de que quando tentou NH entregar a mensagem para PNS, Ocorreu um erro, por exemplo, o tamanho de carga útil excedido o máximo permitido pela PNS ou as credenciais configuradas no NH são inválido etc. Para obter uma visão os erros PNS, podemos ter introduzida uma propriedade denominada [EnableTestSend funcionalidade]. Esta propriedade está ativada automaticamente quando envia mensagens de teste do cliente do Visual Studio ou portal e, consequentemente, permite-lhe ver informações detalhadas de depuração. Pode utilizar esta opção através do APIs tendo o exemplo do .NET SDK onde está disponível agora e será adicionada para todos os cliente SDK eventualmente. Para utilizar este com a chamada resto, simplesmente acrescentar um parâmetro da cadeia de consulta denominado "Testar" no final da sua chamada enviar, por exemplo 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Exemplo (.NET SDK)*
 
Suponha que estiver a utilizar o .NET SDK para enviar uma notificação de alerta nativas:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State`irá simplesmente estado `Enqueued` no final da execução sem qualquer visão o que aconteceu ao seu push. Agora, pode utilizar o `EnableTestSend` propriedade booleana durante a inicialização a `NotificationHubClient` e pode obter o estado detalhado sobre os erros PNS encontrou ao enviar notificação. A chamada enviar aqui irá demorar mais tempo para devolver uma vez que apenas é devolver depois de NH tem entregue a notificação de PNS para determinar o resultado. 
 
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
    
    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);
    
    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Exemplo de resultado*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Esta mensagem indica uma das credenciais inválidas estão configuradas no centro do notificação ou um problema com os registos do concentrador e o curso recomendado seria eliminar este registo e permitir que o cliente de o recriar antes de enviar a mensagem. 
 
> [AZURE.NOTE] Note que a utilização desta propriedade é fortemente limitada e por isso, tem apenas de utilizar este ambiente Dev Center/testar com conjunto de registos limitado. Vamos apenas enviar notificações de depuração 10 dispositivos. Recomendamos também têm um limite de processamento depuração envia para ser 10 por minuto. 

###<a name="review-telemetry"></a>Rever telemetria 

1. **Utilizar o Portal clássico Azure**

    O portal permite-lhe obter uma descrição geral do toda a actividade no seu centro de notificação. 
    
    um) a partir do separador de "dashboard" pode ver uma vista agregada da registos, notificações, bem como erros por plataforma. 
    
    ![][5]
    
    b) também pode adicionar muitas outras plataforma específico métricas do separador "Monitor" para veja mais aprofundada, sobretudo, qualquer erros específicos da PNS devolvido quando tenta NH enviar a notificação para o PNS. 
    
    ![][6]
    
    c) deve iniciar com rever as **Mensagens a receber**, **Operações de registo**, **Notificações efetuada com êxito** e, em seguida, aceda a por separador de plataforma para rever os erros específicos PNS. 
    
    d) se tiver o concentrador de notificação mal configurado com as definições de autenticação, em seguida, verá o erro de autenticação PNS. Esta é uma boa indicação para verificar as credenciais PNS. 

2) **Acesso de programação**

Obter mais detalhes aqui- 

- [Acesso de telemetria de programação]
- [Acesso de telemetria através do exemplo APIs] 

> [AZURE.NOTE] Funcionalidades relacionadas com várias telemetria como **Registos de exportação/importação**, **Acesso de telemetria via APIs** etc só estão disponíveis na camada padrão. Se tentar utilizar estas funcionalidades se estiver na Free ou camada básica, em seguida, obterá exceção mensagem para esse efeito ao utilizar o SDK e um 403 HTTP (proibido) quando utilizá-los diretamente a partir do API do resto. Certifique-se de que foram descontinuadas até padrão camada através do Portal clássica Azure.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Descrição geral de concentradores de notificação]: notification-hubs-push-notification-overview.md
[Tutoriais de introdução]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Orientação de modelo]: https://msdn.microsoft.com/library/dn530748.aspx 
[Orientação de APNS]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Orientações GCM]: http://developer.android.com/google/gcm/adv.html
[Registos de exportação/importação]: http://msdn.microsoft.com/library/dn790624.aspx
[ServiceBus Explorer]: http://msdn.microsoft.com/library/dn530751.aspx
[Código de ServiceBus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Descrição geral do Explorador de servidor VS]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Vs. no servidor Explorer mensagem no blogue - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Vs. no servidor Explorer mensagem no blogue - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[Funcionalidade de EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Acesso de telemetria de programação]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Acesso de telemetria através do exemplo APIs]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 