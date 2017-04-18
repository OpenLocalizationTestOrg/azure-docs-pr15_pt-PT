<properties 
    pageTitle="Enviar notificação personalizada com Azure Mobile Cativação" 
    description="Como enviar notificações personalizadas, incluindo informações de perfil de utilizador nas notificações, como os respetivos nomes"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="personalize-notifications-by-including-user-name"></a>Personalizar as notificações ao incluir o nome de utilizador

No seu pedido para tornar as notificações-los mais apelativos aos seus utilizadores da aplicação, deverá tomar em consideração personalizá-los. Uma abordagem poderosa compreende de seletivamente utilizar as aplicação os nomes dos utilizadores para abordar as notificações para torná-las mais pessoal. Uma palavra de cuidado aqui -, deverá abordar adicionar nomes de utilizador para as notificações cuidadosamente uma vez que se que excesso, esta estratégia, em seguida,-pode deparar-se nos como arrepiante para alguns utilizadores de aplicação. Deverá também Certifique-se de que está a permitir que o utilizador optar ativamente por participar no fornecer estes detalhes pessoais para si com consentimento completo na aplicação móvel antes de começar a utilizar este. 

Tecnicamente, com o Azure Mobile Cativação, que pode efetuar personalizar as notificações ao seguir os passos abaixo na qual utilizamos o cenário de incluir o nome de utilizador nas notificações. Irá utilizar o conceito de informações da App ou etiquetas cujos valores quer podem ser transmitidos dos SDK integrada na aplicação móvel ou através de APIs. Estes Infos aplicação ou etiquetas, em seguida, podem ser utilizadas:

1. para filtragem de notificações a utilizadores específicos com base em valores das informações de aplicação ou 
2. como as notificações de marcadores de posição que será substituído pelo valores específicos ao dispositivo/utilizador durante o envio de notificações a esse dispositivo. 

> [AZURE.IMPORTANT] Tenha em atenção que a velocidade de envio de notificações irá ver uma diminuição devido a este processamento adicional de substituição de valores de informações da app com cada notificações. 

##<a name="register-app-info-in-the-mobile-engagement-portal"></a>Registar informações App no Portal do Cativação móvel

1) Com que começa registar informações App ou de etiquetas no portal do Azure. Aceda a **Definições** -> **etiqueta (informações App)** para que esta.  

![][1]  

2) Clique em **Nova** etiqueta (informações app) e fornecer o nome como *nomedoutilizador* e o tipo de *cadeia* e clique em **Submeter**. 

![][2]

3) Por fim irá ver esta informações app registada como o seguinte:

![][3]

##<a name="send-app-info-from-the-client-sdk"></a>Enviar informações de aplicação a partir do cliente do SDK

Aqui que estamos a utilizar o exemplo de aplicação Windows Universal mas existem de métodos equivalentes para os nossos outras SDK também. 

Partindo do princípio de que tem um método de na aplicação móvel onde obter as informações de perfil de utilizador, como os respetivos nomes provavelmente depois de autenticá-las, irá efetuar a chamada `SendAppInfo` método aqui e povoar o valor da `user_name` informações app que registou anterior para o serviço do Mobile Cativação back-end. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##<a name="send-personalized-notifications"></a>Enviar notificações personalizadas

Agora, está todos definidos para enviar notificações utilizando este **nomedoutilizador**. 

1) Aceda ao Portal de Cativação Mobile no separador **atinja** para criar uma notificação e pode utilizar este marcador de posição no seguinte formato em qualquer lugar no título da notificação ou no corpo. 

![][4]  

> [AZURE.NOTE] Todos os utilizadores para o qual as informações de aplicação nomedoutilizador não estiver definida, não irá obter qualquer notificação. Se a execução da campanha de notificação no modo de teste e se não tiver definida, em seguida, vamos enviar informações da app '?' caráter para substituir o marcador de posição. 

2) Quando Cativação móvel irá selecionar um dispositivo para enviar esta notificação, em seguida, será observe este informações app e substitua o valor no marcador de posição.  
Por exemplo, se podemos ter definido `str = "Scott"` para um utilizador que o dispositivo registo receberão associado com as informações de aplicação de **nomedoutilizador = SCOTT** para este utilizador e este utilizador, irão ver uma fora da notificação de emissão de aplicação no seguinte formato. 

![][5]  

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

