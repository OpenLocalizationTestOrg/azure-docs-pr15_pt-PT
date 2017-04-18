<properties
    pageTitle="Como fazer um chamada telefónica do Twilio (PHP) | Microsoft Azure"
    description="Saiba como efetuar uma chamada e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Amostras destinam-se a aplicação PHP."
    documentationCenter="php"
    services=""
    authors="devinrader"
    manager="twilio"
    editor="mollybos"/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="11/25/2014"
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Como fazer um chamada telefónica utilizando Twilio numa aplicação PHP no Azure

O exemplo seguinte mostra-lhe como pode utilizar Twilio para efetuar uma chamada de uma página web PHP alojado no Azure. A aplicação resultante irá pedir ao utilizador para valores de chamada telefónica, conforme apresentado na seguinte captura de ecrã.

![Formulário de chamada Azure utilizando Twilio e PHP][twilio_php]

Terá de fazer o seguinte para utilizar o código neste tópico:

1. Adquirir uma conta de Twilio e autenticação token. Para começar com Twilio, avaliar preços na [http://www.twilio.com/pricing][twilio_pricing]. Pode inscrever-se para uma conta de avaliação [https://www.twilio.com/try-twilio][try_twilio]. Para obter informações sobre a API fornecida pela Twilio, consulte o artigo [http://www.twilio.com/api][twilio_api].
2. Obter a [biblioteca de Twilio para PHP](https://github.com/twilio/twilio-php) ou instalá-la como um pacote de PEREIRAS. Para mais informações, consulte o [ficheiro Leia-me](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instale o SDK do Azure para PHP. Para obter uma descrição geral da SDK e instruções sobre como instalá-lo, consulte [configurar o SDK do Azure para PHP][setup_php_sdk].

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário web para fazer uma chamada

O seguinte código HTML mostra como criar uma página web (**callform.html**) que obtém dados de utilizador para fazer uma chamada:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Criar o código para efetuar uma chamada
O código seguinte mostra como criar uma página web (**makecall.php**) denominada quando o utilizador submete o formulário apresentado ao **callform.html**. O código mostrado abaixo cria a mensagem chamada e gera a chamada. (Utilize a conta do Twilio e autenticação token em vez dos valores de marcador de posição atribuídos ao **$sid** e **$token** no código abaixo).

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number,
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Para além de fazer a chamada, **makecall.php** apresenta alguns metadados de chamada (por exemplo apresentado na captura de ecrã abaixo). Para mais informações sobre os metadados de chamada, consulte o artigo [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Resposta de chamada Azure utilizando Twilio e PHP][twilio_php_response]

## <a name="run-the-application"></a>Executar a aplicação
O passo seguinte é implementar a aplicação para sites públicos do Azure. Os artigos seguintes contêm as informações para criar um Web site e implementar o seu código com Git, FTP ou WebMatrix (embora não todas as informações de cada artigo relevantes):

* [Criar um Web Site do PHP MySQL Azure e implementar utilizando Git][website-git]
* [Criar um Site de Azure Web PHP MySQL e implementar utilizando FTP][website-ftp]

## <a name="next-steps"></a>Próximos passos
Este código foi fornecido para confirmar que a funcionalidade básica utilizando Twilio no PHP no Azure. Antes de implementar para Azure na produção, poderá querer adicionar mais processamento de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar blobs do Azure armazenamento ou base de dados SQL para armazenar números de telefone e chamada de texto. Para obter informações sobre como utilizar blobs do Azure armazenamento no PHP, consulte o artigo [Utilizar o armazenamento do Windows Azure com aplicações PHP][howto_blob_storage_php]. Para obter informações sobre como utilizar a base de dados SQL no PHP, consulte o artigo [Utilizar base de dados SQL com aplicações PHP][howto_sql_azure_php].
* O código de **makecall.php** utiliza Twilio fornecido pela URL ([http://twimlets.com/message][twimlet_message_url]) para fornecer uma resposta Twilio Markup Language (TwiML) que informa Twilio como continuar a chamada. Por exemplo, o que é devolvido TwiML pode conter um `<Say>` verbais que resulta em texto falado ao destinatário da chamada. Em vez de utilizar o URL Twilio fornecido pela, poderia construir a sua própria serviço para responder a pedido do Twilio; Para obter mais informações, consulte o artigo [como Twilio utilizar para voz e capacidades de SMS no PHP][howto_twilio_voice_sms_php]. Obter mais informações sobre TwiML podem ser encontradas em [http://www.twilio.com/docs/api/twiml][twiml]e obter mais informações sobre `<Say>` e outros verbos Twilio podem ser encontrados em [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leia as diretrizes de segurança Twilio na [https://www.twilio.com/docs/security][twilio_docs_security].

Para obter informações adicionais sobre Twilio, consulte o artigo [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Consulte também
* [Como utilizar Twilio para voz e capacidades SMS no PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php
