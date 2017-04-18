<properties 
    pageTitle="Como utilizar o serviço de correio electrónico SendGrid (PHP) | Microsoft Azure" 
    description="Saiba como enviar e-mail com o serviço de e-mail SendGrid no Azure. Exemplos de código escritos PHP." 
    documentationCenter="php" 
    services="" 
    manager="sendgrid" 
    editor="mollybos" 
    authors="thinkingserious"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Como utilizar o serviço de E-Mail SendGrid do PHP

Este guia demonstra como efetuar tarefas comuns de programação com o serviço de e-mail SendGrid no Azure. Os exemplos estão escritos em PHP.
Os cenários abrangidos incluam **construir o e-mail**, **Enviar e-mail**e **Adicionar anexos**. Para mais informações sobre SendGrid e envio de e-mail, consulte a secção [Os passos seguintes](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de E-Mail SendGrid?

SendGrid é um [serviço de e-mail baseada na nuvem] que fornece fiável [entrega de e-mail transaccionais], escalabilidade e em tempo real analytics juntamente com APIs flexíveis que facilitam a integração personalizada. Cenários comuns de utilização de SendGrid incluem:

-   Enviar recibos automaticamente aos clientes
-   Administrar distribuição listas para o envio de clientes e-fliers mensais e ofertas especiais
-   Recolha de métricas em tempo real para elementos como correio electrónico bloqueado e capacidade de resposta do cliente
-   Gerar relatórios para ajudar a identificar tendências
-   Inquéritos do cliente de reencaminhamento de chamadas
- Notificações de correio eletrónico a partir da sua aplicação

Para mais informações, consulte o artigo [https://sendgrid.com][].

## <a name="create-a-sendgrid-account"></a>Criar uma conta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Utilizar SendGrid a partir da sua aplicação PHP

Utilizar SendGrid numa aplicação do Azure PHP exige sem especiais codificação ou de configuração. Uma vez que SendGrid é um serviço, pode ser acedido exatamente da mesma forma a partir de uma aplicação na nuvem como o-lo a partir de uma aplicação no local.

## <a name="how-to-send-an-email"></a>Como: enviar uma mensagem de E-Mail

Pode enviar e-mail com SMTP ou Web API fornecida pela SendGrid.

### <a name="smtp-api"></a>SMTP API

Para enviar e-mail com a API do SMTP SendGrid, utilize *Swift Mailer*, uma biblioteca baseada em componentes para enviar e-mails a partir de aplicações PHP. Pode transferir a biblioteca de *Swift Mailer* do [http://swiftmailer.org/download][] v5.3.0 (utilize [Compositor] instalar Swift Mailer). Enviar e-mail com a biblioteca envolve a criação de instâncias da <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_Mailer</span>, e <span class="auto-style2">Swift\_mensagem</span> classes, definir as propriedades apropriadas e chamar a <span class="auto-style2">Swift\_Mailer::send</span> método.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>Web API

Utilize o [curl função do PHP][] para enviar e-mail com a API do Web SendGrid.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

Web API da SendGrid é muito semelhante a um REST API, apesar de não é realmente um API RESTful uma vez que, na maioria das chamadas, ambos obter e verbos mensagem podem ser utilizados alternadamente.

## <a name="how-to-add-an-attachment"></a>Como: adicionar um anexo

### <a name="smtp-api"></a>SMTP API

Enviar um anexo de utilizar a API SMTP envolve uma linha de código para o exemplo de script para enviar uma mensagem de e-mail com Swift Mailer adicional.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

A linha de código adicional é da seguinte forma:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

O método de anexar a chamadas desta linha de código na <span class="auto-style2">Swift\_mensagem</span> do objeto e utiliza método estático <span class="auto-style2">fromPath</span> na <span class="auto-style2">Swift\_anexo</span> escolares para obter e anexar um ficheiro a uma mensagem.

### <a name="web-api"></a>Web API

Enviar um anexo de utilizar a API Web é muito semelhante a enviar um e-mail com a API Web. No entanto, tenha em atenção que no exemplo que se segue, a matriz de parâmetro tem de conter este elemento:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Exemplo:

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: utilizar filtros para activar rodapés, rastreio e análise

SendGrid fornece uma funcionalidade de e-mail adicionais através da utilização de 'filtros'. Estas são as que podem ser adicionados a uma mensagem de correio eletrónico para ativar a funcionalidade específica como ativar, clique em controlo, Google analytics, controlo de subscrição e assim sucessivamente.

Filtros podem ser aplicados a uma mensagem, utilizando a propriedade de filtros. Cada filtro é especificado por um hash que contém as definições específicas do filtro. O exemplo seguinte permite que o filtro de rodapé e especifica uma mensagem de texto que será anexada para a parte inferior da mensagem de e-mail.
Para este exemplo utilizamos [sendgrid php biblioteca].
Utilize [Compositor] para instalar a biblioteca:
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Exemplo:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para o serviço de E-Mail SendGrid, siga estas ligações para mais informações.

-   Documentação SendGrid: <https://sendgrid.com/docs>
-   Biblioteca de SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
-   SendGrid ofertas especiais para os clientes do Azure: <https://sendgrid.com/windowsazure.html>

Para mais informações, consulte o artigo também do [Centro de programadores do PHP](/develop/php/).


  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [special offer]: https://www.sendgrid.com/windowsazure.html
  [Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [função de Laço]: http://php.net/curl
  [serviço de e-mail baseada na nuvem]: https://sendgrid.com/email-solutions
  [entrega de e-mail transaccionais]: https://sendgrid.com/transactional-email
  [biblioteca de sendgrid php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Compositor]: https://getcomposer.org/download/
