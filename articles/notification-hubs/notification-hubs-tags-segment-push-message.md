<properties
    pageTitle="Encaminhamento e expressões de etiqueta"
    description="Este tópico explica encaminhamento e tag expressões para concentradores notificação Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="routing-and-tag-expressions"></a>Expressões de encaminhamento e de etiqueta

##<a name="overview"></a>Descrição geral

Etiqueta expressões permitem-lhe para conjuntos de dispositivos ou mais especificamente registos específicos destino ao enviar uma notificação de emissão através de notificação concentradores.


## <a name="targeting-specific-registrations"></a>Filtragem de registos específicos

A única forma de destino específica notificação de registos associá-las, etiquetas, em seguida, assinale essas etiquetas. Tal como é abordado na [Gestão de registo](notification-hubs-push-notification-registration-management.md), para receberem notificações push uma aplicação tem de registar uma alça de dispositivo num concentrador de notificação. Assim que um registo é criado um concentrador de notificação, o back-end aplicação pode enviar notificações push para o mesmo.
O aplicação back-end pode escolher os registos para destino com uma notificação específica das seguintes formas:

1. **Difundir**: todos os registos no centro do notificação recebem a notificação.
2. **Etiqueta**: todos os registos que contêm a etiqueta especificada recebem a notificação.
3. **Expressão de etiqueta**: todos os registos cujo conjunto de etiquetas de corresponder a expressão especificada recebem a notificação.

## <a name="tags"></a>Etiquetas

Uma etiqueta pode ser qualquer cadeia, até 120 carateres, que contém alfanuméricos e os seguintes carateres não-alfanuméricos: '_', ‘@’, '#', '. ',':', '-'. O exemplo seguinte mostra uma aplicação a partir do qual pode receber notificações de alerta sobre os grupos de música específico. Neste cenário, uma forma simple de notificações de rota é aos registos de etiqueta com etiquetas que representam bandas de diferentes, tal como a seguinte imagem.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Nesta imagem, a mensagem com tags **Beatles** atinge apenas o tablet que registado com a etiqueta **Beatles**.

Para obter mais informações sobre como criar registos de etiquetas, consulte [Gestão de registo](notification-hubs-push-notification-registration-management.md).

Pode enviar notificações para etiquetas utilizando os métodos de notificações de envio do `Microsoft.Azure.NotificationHubs.NotificationHubClient` classe no [Microsoft Azure notificação concentradores](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Também pode utilizar Node.js ou APIs dos restantes de notificações de emissão.  Eis um exemplo utilizando o SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Etiquetas não tem de ser previamente aprovisionada e podem referir-se vários os conceitos de aplicação específicas. Por exemplo, os utilizadores desta aplicação exemplo podem comentar faixas e pretende receber toasts, não só para os comentários no seus favoritos faixas, mas também para todos os comentários de seus amigos, independentemente da faixa que são comentários. A imagem seguinte mostra um exemplo deste cenário:



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Nesta imagem Alice está interessada em atualizações para o Beatles e o Rodrigo é interessado em atualizações para os Wailers. O Rodrigo também está interessado em comentários de Carlos e Carlos está na está interessado nos Wailers. Quando uma notificação é enviada para o comentário de Carlos no Beatles, Alice e o Rui recebê-lo.

Enquanto pode descodificar vários preocupações em etiquetas (por exemplo, "band_Beatles" ou "follows_Charlie"), etiquetas são cadeias simples e não as propriedades de com valores. Um registo é correspondido apenas no estado de presença ou ausência de uma etiqueta específica.

Para obter um tutorial completa passo a passo sobre como utilizar etiquetas para enviar a grupos de juros, consulte o artigo [Força de notícias](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).


## <a name="using-tags-to-target-users"></a>Utilizar etiquetas para os utilizadores de destino

É outra forma de utilizar etiquetas identificar todos os dispositivos de um utilizador específico. Os registos podem ser marcados com uma etiqueta que contém um id de utilizador, tal como a seguinte imagem:


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Nesta imagem, a mensagem tagged uid:Alice atingir todos os registos com tags uid:Alice; Por conseguinte, todos os dispositivos de Alice.


##<a name="tag-expressions"></a>Expressões de etiqueta

Existem casos em que uma notificação tem a filtragem de um conjunto de registos que é identificado não por uma única etiqueta, mas por uma expressão booleana em etiquetas.

Considere a hipótese de uma aplicação de desportos que envia um lembrete para todos na Boston sobre um jogo entre a vermelho Sox e Cardinals em. Se a aplicação de cliente regista etiquetas sobre interesse em equipas e localização, a notificação deve ser alvo a todos os participantes Boston que está interessado em Sox a vermelho ou os Cardinals em. Esta condição pode ser expressa com a seguinte expressão booleana:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Etiqueta expressões podem conter todos os operadores lógicos, tais como AND (& &), ou (|) e não (!). Também podem conter parênteses. Etiqueta expressões estão limitados a 20 etiquetas se contiverem apenas ORs; caso contrário, estão limitados a 6 etiquetas.

Eis um exemplo para o envio de notificações com as expressões de etiqueta utilizando o SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)"; 

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
