<properties 
    pageTitle="Como utilizar notificação concentradores com PHP" 
    description="Saiba como utilizar concentradores de notificação do Azure a partir de um valor de erro PHP back-end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="php" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/07/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-php"></a>Como utilizar concentradores de notificação de PHP
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Pode aceder a todas as funcionalidades de notificação concentradores a partir de um back-end PHP/Java/Rubi utilizando a interface do resto do concentrador de notificação, tal como descrito no tópico MSDN [Notificação concentradores REST APIs](http://msdn.microsoft.com/library/dn223264.aspx).

Este tópico mostramos-como:

* Criar um cliente do resto concentradores de notificação para funcionalidades no PHP;
* Siga o [tutorial de introdução de obter](notification-hubs-ios-apple-push-notification-apns-get-started.md) para a sua plataforma móvel da escolha, implementar a parte de back-end no PHP.

## <a name="client-interface"></a>Interface do cliente
A interface de cliente principal pode fornecer os mesmos métodos que estão disponíveis no [.NET notificação concentradores SDK](http://msdn.microsoft.com/library/jj933431.aspx), isto irá permitir-lhe traduzir diretamente a tutoriais e amostras atualmente disponíveis neste site e contribuiu por da Comunidade na internet.

Pode localizar o código disponível na [amostra de bombom PHP restantes].

Por exemplo, para criar um cliente:

    $hub = new NotificationHub("connection string", "hubname"); 

Para enviar um iOS nativa notificação:
    
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Pós-implementação
Se ainda não o fez, siga os nossos [tutorial introdução Get] até a última secção onde tem para implementar o back-end.
Além disso, se pretender que pode utilizar o código do [exemplo de bombom PHP resto] e ir diretamente para a secção [Concluir o tutorial](#complete-tutorial) .

Todos os detalhes para implementar um dispositivo de moldagem completo do resto podem ser encontrados no [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Nesta secção Descrevemos a aplicação PHP dos passos principais necessárias para aceder a pontos finais de notificação concentradores resto:

1. Analisar a cadeia de ligação
2. Gerar o token de autorização
3. Efetuar a chamada HTTP

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

Eis a execução do cliente, cujo construtor que analisa a cadeia de ligação de classe principal:

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";
    
        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;
    
        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;
    
            $this->parseConnectionString($connectionString);
        }
    
        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }
    
            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>Criar token de segurança
Os detalhes da criação de token de segurança estão disponíveis [aqui](http://msdn.microsoft.com/library/dn495627.aspx).
O seguinte método tem de ser adicionadas à classe **NotificationHub** para criar o token com base no URI do pedido atual e as credenciais extraídas de uma cadeia de ligação.

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>Enviar uma notificação
Em primeiro lugar, diga-nos defina uma classe que representa uma notificação.

    class Notification {
        public $format;
        public $payload;
    
        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;
    
        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }
    
            $this->format = $format;
            $this->payload = $payload;
        }
    }

Esta classe é um contentor para corpo de uma notificação nativo, ou um conjunto de propriedades, no caso de uma notificação de modelo e um conjunto de cabeçalhos que contém o formato (plataforma nativa ou modelo) e específico da plataforma propriedades (como a propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a [documentação de notificação concentradores REST APIs](http://msdn.microsoft.com/library/dn495827.aspx) e formatos dos plataformas de notificação específico para todas as opções disponíveis.

Com esta classe pelas, podemos pode agora escrever enviar métodos de notificação dentro a classe de **NotificationHub** .

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }
        
        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

Os métodos acima enviar um pedido de HTTP POST para o ponto final /messages do seu centro de notificação, com o corpo correto e os cabeçalhos para enviar a notificação.

##<a name="complete-tutorial"></a>Concluir o tutorial
Agora pode concluir o tutorial de introdução ao enviar notificação a partir de um valor de erro PHP back-end.

Iniciar o cliente de notificação concentradores (SUBST o nome da cadeia e concentrador ligação como instruções no [tutorial de introdução de obter]):

    $hub = new NotificationHub("connection string", "hubname"); 

Em seguida, adicione o código de enviar dependendo da sua plataforma móvel de destino.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Da loja Windows e o Windows Phone 8.1 (que não sejam Silverlight)

    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS

    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight

    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Executar o seu código PHP deve produzir agora uma notificação não apareça no seu dispositivo de destino.


## <a name="next-steps"></a>Próximos passos
Neste tópico que lhe foram mostradas como criar um cliente Java resto simple para concentradores de notificação. A partir daqui, pode:

* Transfira total [amostra de bombom PHP resto], que contém o código acima.
* Continuar a obter informações sobre concentradores notificação etiquetagem de funcionalidade [tutorial de notícias força]
* Saiba mais sobre a instalação push notificações que os utilizadores individuais [tutorial notificar utilizadores]

Para obter mais informações, consulte também o [Centro de programadores do PHP](/develop/php/).

[Exemplo de bombom PHP resto]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Obter tutorial de introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
 
