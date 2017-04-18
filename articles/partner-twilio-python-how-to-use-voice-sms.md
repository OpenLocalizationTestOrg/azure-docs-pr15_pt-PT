<properties
    pageTitle="Como utilizar Twilio para voz e SMS (PHP) | Microsoft Azure"
    description="Saiba como efetuar uma chamada e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos PHP."
    services=""
    documentationCenter="python"
    authors="devinrader"
    manager="twilio"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python" 
    ms.topic="article"
    ms.date="02/19/2015"
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Como utilizar Twilio para voz e capacidades SMS no PHP
Este guia demonstra como efetuar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abrangidos incluem efetuar uma chamada e enviar uma mensagem curta mensagem serviço (SMS). Para mais informações sobre como Twilio e utilizar voz e SMS nas suas aplicações, consulte a secção [Os passos seguintes](#NextSteps) .

## <a id="WhatIs"></a>O que é Twilio?
Twilio suporta o futuro das comunicações de negócio, permitindo que programadores incorporar voz, VoIP e mensagens em aplicações. Estes virtualizar em todos os infraestrutura necessário num ambiente baseado na nuvem, global, expor-lo através da plataforma de comunicações API Twilio. As aplicações são simples criar e dimensionáveis. Desfrute flexibilidade consigo pagamento-como aceda preços e beneficiem das vantagens dos fiabilidade na nuvem.

**Voz Twilio** permite que as suas aplicações para o fazer e receber chamadas telefónicas. **Twilio SMS** permite que a sua aplicação para enviar e receber mensagens de texto. **Cliente Twilio** permite-lhe efetuar chamadas VoIP a partir de qualquer telefone, um tablet ou um browser e suporta WebRTC.

## <a id="Pricing"></a>Twilio preços e ofertas especiais

Os clientes Azure receber [ofertas especiais] $10 do Twilio crédito quando atualizar a sua conta Twilio. Este crédito Twilio podem ser aplicado a qualquer a utilização de Twilio (crédito $10 equivalente a enviar até 1.000 mensagens SMS ou receber até 1000 entrados minutos de voz, dependendo da localização do seu destino de número e a mensagem ou chamada de telefone). Resgatar esta crédito Twilio e introdução ao: [ahoy.twilio.com/azure].

Twilio é um serviço repartição. Não existe nenhuma tarifas de configuração da compilação e pode fechar a sua conta em qualquer altura. Pode encontrar mais detalhes no [Twilio preços] [twilio_pricing].

## <a id="Concepts"></a>Conceitos
Twilio API é um API RESTful que fornece voz e funcionalidade do SMS para aplicações. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte o artigo [Twilio API bibliotecas] [twilio_libraries].

Chaves aspetos da Twilio API são Twilio verbos e Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Verbos Twilio
API garante a utilização de Twilio verbos; Por exemplo, o ** &lt;diga&gt; ** verbais instrui Twilio pronunciadas entregar uma mensagem a fazer uma chamada.

Segue-se uma lista de Twilio verbos. Saiba mais sobre os verbos e capacidades através do [documentação Twilio Markup Language] [http://www.twilio.com/docs/api/twiml].

* ** &lt;Acesso telefónico&gt;**: liga-se o autor da chamada para outro telefone.
* ** &lt;Reunir&gt;**: recolhe dígitos numéricos introduzidos no teclado do telefone.
* ** &lt;Desligar&gt;**: termina uma chamada.
* ** &lt;Reproduzir&gt;**: é reproduzido um ficheiro de áudio.
* ** &lt;Pausa&gt;**: aguarda silenciosamente durante um número especificado de segundos.
* ** &lt;Registo&gt;**: regista a voz do autor da chamada e devolve um URL de um ficheiro que contém a gravação.
* ** &lt;Redirecionar&gt;**: transfere o controlo de uma chamada ou SMS para TwiML num URL diferente.
* ** &lt;Rejeitar&gt;**: rejeitar uma chamada a receber para o número do Twilio sem faturação
* ** &lt;Diga&gt;**: converte texto em voz que é feito a fazer uma chamada.
* ** &lt;Sms&gt;**: envia uma mensagem de SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseado em XML com base em verbos Twilio que o informem Twilio de como a processar uma chamada ou SMS.

Por exemplo, a seguinte TwiML seria converter o texto **Olá mundo** voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando a aplicação chama Twilio API, um dos parâmetros API é o URL que devolve a resposta TwiML. Para fins de desenvolvimento, pode utilizar Twilio fornecido pela URLs para fornecer as respostas de TwiML utilizadas pelas suas aplicações. Também poderia alojar o seus próprio URLs para produzir as respostas de TwiML e outra opção é utilizar o objeto **TwiMLResponse** .

Para obter mais informações sobre Twilio verbos, os respetivos atributos e TwiML, consulte o artigo [TwiML] [twiml]. Para obter informações adicionais sobre a API do Twilio, consulte o artigo [Twilio API] [twilio_api].

## <a id="CreateAccount"></a>Criar uma conta de Twilio
Quando estiver pronto para obter uma conta de Twilio, inscrever-se ao [Tentar Twilio] [try_twilio]. Pode começar com uma conta gratuita e atualizar a sua conta mais tarde.

Ao inscrever-se para uma conta de Twilio, receberá um ID de conta e um token de autenticação. Ambos serão necessárias para efetuar chamadas de Twilio API. Para evitar acesso não autorizado à sua conta, manter token de autenticação seguro. O ID da conta e autenticação token podem ser visualizados na [página de conta Twilio] [twilio_account], nos campos denominada **SID da conta** e **AUTH TOKEN**, respetivamente.

## <a id="create_app"></a>Criar uma aplicação de PHP
Uma aplicação de PHP que utiliza o serviço de Twilio e está em execução no Azure é em qualquer outra aplicação PHP que utiliza o serviço de Twilio. Enquanto os serviços de Twilio são baseados no resto e podem ser chamados de PHP de várias formas, este artigo irá focar-se sobre como utilizar os serviços de Twilio com [Twilio biblioteca para PHP a partir do GitHub][twilio_php]. Para obter mais informações sobre como utilizar a biblioteca de Twilio para PHP, consulte o artigo [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Instruções detalhadas sobre como criar e implementar uma aplicação Twilio/PHP Azure está disponíveis no [como fazer Twilio de utilizar uma chamada telefónica numa aplicação PHP no Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurar a aplicação para utilizar bibliotecas de Twilio
Pode configurar a aplicação para utilizar a biblioteca de Twilio para PHP de duas maneiras:

1. Transferir a biblioteca de Twilio para PHP a partir do GitHub ([https://github.com/twilio/twilio-php][twilio_php]) e adicione o directório **dos serviços** para a sua aplicação.

    -OU-

2. Instale a biblioteca de Twilio para PHP como um pacote de PEREIRAS. Pode ser instalado com os seguintes comandos:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Assim que tiver instalado a biblioteca de Twilio para PHP, em seguida, pode adicionar uma instrução **require_once** na parte superior dos seus ficheiros PHP para fazer referência a biblioteca:

        require_once 'Services/Twilio.php';

Para obter mais informações, consulte o artigo [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Como: efetuar uma chamada de saída
A imagem seguinte mostra como efetuar uma chamada de saída utilizando a classe de **Services_Twilio** . Este código utiliza também um site Twilio fornecido para devolver a resposta Twilio Markup Language (TwiML). Substitua os valores para os números de telefone **de** e **até** e certifique-se de que verifique se o número de telefone **a partir** da sua conta Twilio antes de executar o código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Tal como mencionado, este código utiliza um site de Twilio fornecido para devolver a resposta TwiML. Em vez disso, pode utilizar o seu próprio site para fornecer a resposta TwiML; Para mais informações, consulte o artigo [como fornecer TwiML respostas a partir do seu próprio Web Site](#howto_provide_twiml_responses).


- **Nota**: para resolver problemas de erros de validação de certificado SSL, consulte o artigo [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]


## <a id="howto_send_sms"></a>Como: enviar uma mensagem SMS
A imagem seguinte mostra como enviar uma mensagem SMS utilizando a classe de **Services_Twilio** . O número **de** é fornecido pelo Twilio para contas de avaliação para enviar mensagens SMS. **O número** tem de ser validado para a sua conta Twilio antes de executar o código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Como: fornecer respostas TwiML do seu próprio Web site
Quando a aplicação inicia uma chamada à Twilio API, Twilio irá enviar o seu pedido para um URL que esperado para devolver uma resposta TwiML. O exemplo acima utiliza o URL Twilio fornecido pela [http://twimlets.com/message][twimlet_message_url]. (Enquanto TwiML foi concebido para utilização ao Twilio, pode ver o it no seu browser. Por exemplo, clique em [http://twimlets.com/message] [ twimlet_message_url] para ver uma esvaziar `<Response>` elemento; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] para ver uma `<Response>` elemento que contém uma `<Say>` elemento.)

Em vez de depender do URL Twilio fornecido pela, pode criar o seu próprio site que devolve respostas HTTP. Pode criar o site de qualquer idioma que devolve as respostas XML. Este tópico assume que irá utilizar PHP para criar o TwiML.

Página PHP seguinte resulta no envio uma resposta TwiML que diz **Olá mundo** na chamada.

    <?php
        header("content-type: text/xml");
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>
        <Say>Hello world.</Say>
    </Response>

Como pode ver do exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca de Twilio para PHP contém classes que irão gerar TwiML por si. O exemplo abaixo produz a resposta equivalente, conforme mostrado acima, mas utiliza o **serviços\_Twilio\_Twiml** escolares na biblioteca de Twilio para PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para mais informações sobre TwiML, consulte o artigo [https://www.twilio.com/docs/api/twiml][twiml_reference].

Assim que tiver a sua página PHP definida para fornecer respostas TwiML, utilize o URL da página PHP quando passou o URL para o `Services_Twilio->account->calls->create` método. Por exemplo, se tiver uma aplicação Web com o nome **MyTwiML** implementado um Azure alojado serviço e o nome da página PHP é **mytwiml.php**, pode ser passado o URL para **Services_Twilio -> conta -> chamadas -> criar** conforme mostrado no exemplo seguinte:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Para obter informações adicionais sobre como utilizar Twilio no Azure com PHP, consulte o artigo [como fazer Twilio de utilizar uma chamada telefónica numa aplicação PHP no Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Como: utilizar os serviços de Twilio adicionais
Para além dos exemplos apresentados aqui, Twilio oferece APIs baseada na web que pode utilizar para tirar partido de funcionalidades Twilio adicionais a partir da sua aplicação Azure. Para detalhes completos, consulte a [documentação da Twilio API] [twilio_api_documentation].

## <a id="NextSteps"></a>Próximos passos
Agora que aprendeu os princípios básicos para o serviço de Twilio, siga estas ligações para mais informações:

* [Diretrizes de segurança Twilio] [twilio_security_guidelines]
* [How to de Twilio guias e o código de exemplo] [twilio_howtos]
* [Tutoriais Twilio guia de introdução][twilio_quickstarts]
* [Twilio no GitHub] [twilio_on_github]
* [Falar com suporte de Twilio] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
