<properties 
    pageTitle="Como utilizar notificação concentradores com Python" 
    description="Saiba como utilizar concentradores de notificação do Azure a partir de um valor de erro Python back-end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="python" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-python"></a>Como utilizar concentradores de notificação de Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
        
Pode aceder a todas as funcionalidades de notificação concentradores a partir de um back-end Java/PHP/Python/Rubi utilizando a interface do resto do concentrador de notificação, tal como descrito no tópico MSDN [Notificação concentradores REST APIs](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE] Esta é uma implementação de referência de exemplo para implementar o envia notificação no Python e não é o Python SDK beneficiam do notificações concentrador.
>
> Este exemplo é escrito utilizando Python 3.4.

Este tópico mostramos-como:

* Crie um cliente do resto concentradores de notificação para funcionalidades no Python.
* Envie notificações utilizando a interface do Python para a notificação concentrador REST APIs. 
* Obter uma cópia do pedido de HTTP resto/resposta para finalidade depuração/educacionais. 

Pode seguir o [tutorial de introdução de obter](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) para a sua plataforma móvel da escolha, implementar a parte de back-end no Python.

> [AZURE.NOTE] O âmbito da amostra é limitado apenas para enviar notificações e não ações qualquer gestão de registo.

## <a name="client-interface"></a>Interface do cliente
A interface de cliente principal pode fornecer os mesmos métodos que estão disponíveis no [.NET notificação concentradores SDK](http://msdn.microsoft.com/library/jj933431.aspx). Permitirá diretamente traduzir a tutoriais e amostras atualmente disponíveis neste site e contribuiu por da Comunidade na internet.

Pode localizar o código disponível na [amostra de bombom Python restantes].

Por exemplo, para criar um cliente:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
    
Para enviar uma notificação de alerta do Windows:
    
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)
    
## <a name="implementation"></a>Pós-implementação
Se ainda não o fez, siga os nossos [tutorial introdução Get] até a última secção onde tem para implementar o back-end.

Todos os detalhes para implementar um dispositivo de moldagem completo do resto podem ser encontrados no [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Nesta secção Descrevemos a aplicação Python dos passos principais são necessários para aceder a pontos finais de notificação concentradores resto e enviar notificações

1. Analisar a cadeia de ligação
2. Gerar o token de autorização
3. Enviar uma notificação utilizando HTTP REST API

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

Eis a execução do cliente, cujo construtor analisa a cadeia de ligação de classe principal:

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"
    
        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug
    
            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")
    
            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Criar token de segurança
Os detalhes da criação de token de segurança estão disponíveis [aqui](http://msdn.microsoft.com/library/dn495627.aspx).
Os seguintes métodos tem de ser adicionadas à classe **NotificationHub** para criar o token com base no URI do pedido atual e as credenciais extraídas de uma cadeia de ligação.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>Enviar uma notificação utilizando HTTP REST API
Utilização em primeiro lugar, deixe que definir uma classe que representa uma notificação.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
    
            self.format = notification_format
            self.payload = payload
    
            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Esta classe é um contentor para corpo de uma notificação nativo ou um conjunto de propriedades em caso de uma notificação de modelo, um conjunto de cabeçalhos que contém o formato (plataforma nativa ou modelo) e específico da plataforma propriedades (como a propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a [documentação de notificação concentradores REST APIs](http://msdn.microsoft.com/library/dn495827.aspx) e formatos dos plataformas de notificação específico para todas as opções disponíveis.

Agora com esta classe, podemos pode escrever enviar métodos de notificação dentro a classe de **NotificationHub** .

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Os métodos acima enviar um pedido de HTTP POST para o ponto final /messages do seu centro de notificação, com o corpo correto e os cabeçalhos para enviar a notificação.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Utilizando a propriedade de depuração para ativar o registo detalhado
Ativar depuração propriedade durante a inicialização o concentrador de notificação escreverá terminar registo detalhadas sobre o pedido de HTTP e informações de estado da resposta, bem como mensagem de notificação detalhada envia resultado. Adicionámos recentemente esta propriedade denominada [notificação concentradores TestSend propriedade](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) devolve informações detalhadas sobre o resultado de enviar notificação. Para utilizá-lo - inicialização utilizando o seguinte:

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Notificação concentrador enviar pedido HTTP URL obtém acrescentado com uma cadeia de consulta "teste" como resultado. 

##<a name="complete-tutorial"></a>Concluir o tutorial
Agora pode concluir o tutorial de introdução ao enviar notificação a partir de um valor de erro Python back-end.

Iniciar o cliente de notificação concentradores (SUBST o nome da cadeia e concentrador ligação como instruções no [tutorial de introdução de obter]):

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

Em seguida, adicione o código de enviar dependendo da sua plataforma móvel de destino. Este exemplo também adiciona métodos de nível superiores para ativar as notificações de envio com base na plataforma, por exemplo, send_windows_notification para windows; send_apple_notification (para apple) etc. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Da loja Windows e o Windows Phone 8.1 (que não sejam Silverlight)

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight

    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS

    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Executar o seu código Python deve produzir uma notificação não apareça no seu dispositivo de destino.

## <a name="examples"></a>Exemplos:

### <a name="enabling-debug-property"></a>Ativar a propriedade de depuração
Quando ativar o sinalizador de depuração durante a inicialização a NotificationHub, em seguida, irá ver o pedido de HTTP detalhado e informações de estado da resposta, bem como NotificationOutcome semelhante ao seguinte onde possa compreender o que são cabeçalhos HTTP são transmitidos no pedido de e resposta que HTTP foi recebida a partir do concentrador de notificação:    ![][1]

Irá ver detalhadas, por exemplo, o resultado do concentrador de notificação 

- Quando a mensagem é enviada com êxito para o serviço de notificações de emissão. 
    
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Se não tiver havido sem destinos encontrados para todas as notificações push, em seguida, provavelmente passar para ver a seguinte na resposta (que indica que não foram sem registos que se encontram para entregar a notificação de provavelmente porque os registos tinham algumas tags não correspondentes)

        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Difundir a notificação de alerta para Windows 

Repare que os cabeçalhos que são enviados saída quando estiver a enviar uma notificação de alerta de difusão para o cliente do Windows. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Enviar notificação especificando uma etiqueta (ou expressão de etiqueta)

Repare que o cabeçalho de HTTP de etiquetas que é adicionado ao pedido de HTTP (no exemplo abaixo, podemos vai enviar a notificação de apenas para os registos com carga útil de 'desportos')

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Enviar notificação especificar várias etiquetas

Repare que o cabeçalho etiquetas HTTP é alterado quando vários sinalizadores são enviados. 
    
    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Notificação transformada em modelo

Repare que as alterações de cabeçalho de formato HTTP e o corpo de carga útil é enviado como parte do corpo do pedido HTTP:

**Do lado do cliente - modelo registado**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Do lado do servidor - enviar a carga de útil**
        
        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]


## <a name="next-steps"></a>Próximos passos
Neste tópico que lhe foram mostradas como criar um cliente Python resto simple para concentradores de notificação. A partir daqui, pode:

* Transfira total [amostra de bombom Python resto], que contém o código acima.
* Continuar a obter informações sobre concentradores notificação etiquetagem funcionalidade no [tutorial de força de notícias]
* Continuar a obter informações sobre a funcionalidade de notificação concentradores modelos no [tutorial de notícias localizar]

<!-- URLs -->
[Exemplo de bombom Python resto]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Obter tutorial de introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Força tutorial de notícias]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Localizar tutorial de notícias]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 
