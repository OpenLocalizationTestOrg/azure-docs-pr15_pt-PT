<properties
    pageTitle="Enviar as notificações push com concentradores de notificação do Azure e Node.js"
    description="Saiba como utilizar concentradores de notificação para enviar notificações push a partir de uma aplicação de Node.js."
    keywords="notificação de emissão, push notifications,node.js push, push do ios"
    services="notification-hubs"
    documentationCenter="nodejs"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Enviar as notificações push com concentradores de notificação do Azure e Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##<a name="overview"></a>Descrição geral

> [AZURE.IMPORTANT] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Este guia mostrar-lhe como enviar notificações push com a ajuda do Azure notificação concentradores diretamente a partir de uma aplicação de Node.js. 

Os cenários abrangidos incluem o envio de notificações push para aplicações plataformas seguintes:

* Android
* iOS
* Windows Phone
* Plataforma universal Windows 

Para mais informações sobre concentradores de notificação, consulte a secção [Os passos seguintes](#next) .

##<a name="what-are-notification-hubs"></a>O que são notificação concentradores?

Azure notificação concentradores fornecem uma infraestrutura de fácil utilização, plataforma com várias, dimensionável para envio de notificações push para dispositivos móveis. Para obter detalhes sobre a infraestrutura de serviço, consulte a página [Concentradores de notificação do Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

##<a name="create-a-nodejs-application"></a>Criar uma aplicação de Node.js

Neste tutorial, o primeiro passo é criar uma nova aplicação de Node.js em branco. Para obter instruções sobre como criar uma aplicação de Node.js, consulte o artigo [criar e implementar uma aplicação de Node.js para Web Site da Azure][nodejswebsite], [Serviço em nuvem Node.js] [ Node.js Cloud Service] utilizar o Windows PowerShell ou o [Web Site com WebMatrix].

##<a name="configure-your-application-to-use-notification-hubs"></a>Configurar a aplicação para utilizar concentradores de notificação

Para utilizar Azure notificação concentradores, é necessário transferir e utilizar Node.js [pacote azure](https://www.npmjs.com/package/azure), que inclui um conjunto de biblioteca de ajuda que comunicar com os serviços de resto de notificações push incorporado.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilizar o Gestor de pacote nó (NPM) para obter o pacote

1.  Utilize uma interface de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **festa** (Linux) e navegue para a pasta onde a criou a aplicação em branco.

2.  Na janela de comandos, escreva a **npm instalar azure sb** .

3.  Pode executar o comando **ls** ou **dir** para verificar que manualmente uma **nó\_módulos** pasta foi criada. Nessa pasta, localize o pacote do **azure** , que contém as bibliotecas que precisa de aceder ao concentrador de notificação.

>[AZURE.NOTE] Pode saber mais sobre como instalar NPM no oficial [NPM blogue](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 

### <a name="import-the-module"></a>Importar o módulo

Utilizando um editor de texto, adicione o seguinte para a parte superior do ficheiro **server.js** da aplicação:

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Configurar uma ligação de concentrador de notificação do Azure

O objeto **NotificationHubService** permite-lhe trabalhar com concentradores de notificação. O código seguinte cria um objeto de **NotificationHubService** para o concentrador de nofication denominado **hubname**. Adicione-o na parte superior do ficheiro **server.js** , depois da instrução para importar o módulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

O valor da ligação **connectionstring** pode ser obtido a partir do [Portal do Azure] executando os seguintes passos:

1. No painel de navegação esquerdo, clique em **Procurar**.

2. Selecione **Concentradores de notificação**e, em seguida, localize o concentrador que pretende utilizar para a amostra. Pode fazer referência para o [Windows Store introdução tutorial](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) se precisar de ajuda para criar um concentrador de notificação de novo.

3. Selecione **Definições**.

4. Clique em **políticas de acesso**. Irá ver ambas as cadeias de ligação de acesso partilhado e completo.

![Portal Azure - concentradores de notificação](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] Também pode obter a cadeia de ligação utilizando o cmdlet **Get-AzureSbNamespace** fornecido pela [Azure PowerShell](../powershell-install-configure.md) ou o comando **Mostrar azure sb espaço de nomes** com a [Interface de comandos do Azure (Azure CLI)](../xplat-cli-install.md).

##<a name="general-architecture"></a>Arquitetura de geral

O objecto **NotificationHubService** expõe as seguintes instâncias de objeto para o envio de notificações push para dispositivos específicos e de aplicações:

* **Android** - utilizar o objeto **GcmService** , que está disponível em **notificationHubService.gcm**
* **iOS** - utilizar o objeto **ApnsService** , que é acessível na **notificationHubService.apns**
* **Windows Phone** - utilizar o objeto **MpnsService** , que está disponível em **notificationHubService.mpns**
* **Universal plataforma Windows** - utilizar o objeto **WnsService** , que está disponível em **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Como: enviar as notificações push das aplicações do Android

O objeto **GcmService** fornece um método de **Enviar** que pode ser utilizado para enviar notificações push para aplicações Android. O método **Enviar** aceita os seguintes parâmetros:

* **Etiquetas** - o identificador de etiqueta. Não se for fornecida nenhum etiqueta, a notificação será enviada para os clientes de al.
* **Carga útil** - JSON ou carga útil de cadeia observou a mensagem.
* **Chamada de retorno** - a função de chamada de retorno.

Para mais informações sobre o formato de carga útil, consulte a secção de **carga útil** do [Servidor de GCM execução do](http://developer.android.com/google/gcm/server.html#payload) documento.

O código seguinte utiliza a instância de **GcmService** exposta pela **NotificationHubService** para enviar uma notificação de emissão para todos os clientes registados.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Como: enviar as notificações push das aplicações do iOS

Mesmo, tal como acontece com aplicações Android descrita acima, o objeto **ApnsService** fornece um método de **Enviar** que pode ser utilizado para enviar notificações push para aplicações do iOS. O método **Enviar** aceita os seguintes parâmetros:

* **Etiquetas** - o identificador de etiqueta. Não se for fornecida nenhum etiqueta, a notificação será enviada para todos os clientes.
* **Carga útil** - JSON a mensagem ou carga útil de cadeia.
* **Chamada de retorno** - a função de chamada de retorno.

Para mais informações no formato de carga útil, consulte a secção de **Carga útil de notificação** do documento [Local e guia de programação de notificação de notificações Push](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

O código seguinte utiliza a instância de **ApnsService** exposta pela **NotificationHubService** para enviar uma mensagem de alerta para todos os clientes:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Como: enviar as notificações push das aplicações do Windows Phone

O objeto **MpnsService** fornece um método de **Enviar** que pode ser utilizado para enviar notificações push para aplicações do Windows Phone. O método **Enviar** aceita os seguintes parâmetros:

* **Etiquetas** - o identificador de etiqueta. Não se for fornecida nenhum etiqueta, a notificação será enviada para todos os clientes.
* **Carga útil** - carga útil XML a mensagem.
* **Nomedestino**  -  `toast` para notificações de alerta. `token`para as notificações do mosaico.
* **NotificationClass** - a prioridade da notificação. Consulte a secção de **Elementos de cabeçalho de HTTP** do documento [a um servidor de notificações de emissão](http://msdn.microsoft.com/library/hh221551.aspx) para os valores válidos.
* **Opções** - cabeçalhos pedido opcionais.
* **Chamada de retorno** - a função de chamada de retorno.

Para obter uma lista de opções de **NomeDestino**, **NotificationClass** e cabeçalho válidas, consulte a página [a um servidor de notificações de emissão](http://msdn.microsoft.com/library/hh221551.aspx) .

O código de exemplo seguinte utiliza a instância de **MpnsService** exposta pela **NotificationHubService** para enviar uma notificação de push alerta:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Como: enviar as notificações push para aplicações Universal Windows plataforma (UWP)

O objeto **WnsService** fornece um método de **Enviar** que pode ser utilizado para enviar notificações push para aplicações Universal plataforma Windows.  O método **Enviar** aceita os seguintes parâmetros:

* **Etiquetas** - o identificador de etiqueta. Não se for fornecida nenhum etiqueta, a notificação será enviada para todos os clientes registados.
* **Carga útil** - a carga de útil de mensagem XML.
* **Tipo de** -o tipo de notificação.
* **Opções** - cabeçalhos pedido opcionais.
* **Chamada de retorno** - a função de chamada de retorno.

Para obter uma lista de tipos de válido e cabeçalhos de pedido, consulte o artigo [cabeçalhos de pedidos e respostas do serviço de notificações de emissão](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

O código seguinte utiliza a instância de **WnsService** exposta pela **NotificationHubService** para enviar uma notificação de push alerta para uma aplicação UWP:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <a name="next-steps"></a>Próximos passos

Fragmentos o exemplo acima permitem-lhe construir facilmente a infraestrutura de serviço a notificações push para uma grande variedade de dispositivos. Agora que aprendeu os princípios básicos para utilizar notificação concentradores com node.js, siga estas ligações para saber mais sobre como pode expandir estas capacidades ainda mais.

-   Consulte a referência MSDN para [concentradores notificação Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
-   Visite o repositório [Azure SDK para nó] no GitHub para obter mais exemplos e detalhes de implementação.

  [Azure SDK para nó]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Web Site com WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Portal do Azure]: https://portal.azure.com
