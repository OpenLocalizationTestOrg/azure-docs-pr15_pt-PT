<properties
    pageTitle="Como enviar notificações agendadas | Microsoft Azure"
    description="Este tópico descreve utilizar notificações agendada com o Azure notificação concentradores."
    services="notification-hubs"
    documentationCenter=".net"
    keywords="notificações push, notificação de emissão, agendamento notificações push"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="how-to-send-scheduled-notifications"></a>Como: Enviar notificações agendadas


##<a name="overview"></a>Descrição geral

Se tiver um cenário em que que pretende enviar uma notificação a determinada altura no futuro, mas não possui uma forma fácil de activar o seu código de segurança para enviar a notificação. Camada padrão notificação concentradores suporta uma funcionalidade que permite-lhe agendar notificações para cima para 7 dias no futuro.

Quando envia uma notificação, simplesmente utilize a classe de [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) no SDK concentradores notificação conforme mostrado no exemplo seguinte:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Além disso, pode cancelar uma notificação anteriormente agendada utilizando o respetivo notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Não existem limites no número de notificações agendadas que pode enviar.