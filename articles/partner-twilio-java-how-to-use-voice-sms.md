<properties 
    pageTitle="Como utilizar Twilio para voz e SMS (Java) | Microsoft Azure" 
    description="Saiba como efetuar uma chamada e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos Java." 
    services="" 
    documentationCenter="java" 
    authors="devinrader" 
    manager="twilio" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Como utilizar Twilio para voz e capacidades SMS no Java

Este guia demonstra como efetuar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abrangidos incluem efetuar uma chamada e enviar uma mensagem curta mensagem serviço (SMS). Para mais informações sobre como Twilio e utilizar voz e SMS nas suas aplicações, consulte a secção [Os passos seguintes](#NextSteps) .

## <a id="WhatIs"></a>O que é Twilio?
Twilio é um API do serviço web de telefonia que permite-lhe utilizar o seu existente idiomas de web e competências a criação de voz e as aplicações do SMS. Twilio é um serviço de terceiros (não uma funcionalidade Azure e não um produto da Microsoft).

**Voz Twilio** permite que as suas aplicações para o fazer e receber chamadas telefónicas. **Twilio SMS** permite que as suas aplicações para o fazer e receber mensagens SMS. **Twilio cliente** permite que as suas aplicações permitir a comunicação de voz utilizando ligações de Internet existentes, incluindo ligações móvel.

## <a id="Pricing"></a>Twilio preços e ofertas especiais
Obter informações sobre preços Twilio estão disponíveis em [Twilio preços] [twilio_pricing]. Os clientes Azure recebem uma [oferta especial][special_offer]: um crédito gratuito de 1000 textos ou 1000 minutos de entrada. Para se inscrever para esta oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceitos
Twilio API é um API RESTful que fornece voz e funcionalidade do SMS para aplicações. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte o artigo [Twilio API bibliotecas] [twilio_libraries].

Chaves aspetos da Twilio API são Twilio verbos e Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Verbos Twilio
API garante a utilização de Twilio verbos; Por exemplo, o ** &lt;diga&gt; ** verbais instrui Twilio pronunciadas entregar uma mensagem a fazer uma chamada. 

Segue-se uma lista de Twilio verbos.

* ** &lt;Acesso telefónico&gt;**: liga-se o autor da chamada para outro telefone.
* ** &lt;Reunir&gt;**: recolhe dígitos numéricos introduzidos no teclado do telefone.
* ** &lt;Desligar&gt;**: termina uma chamada.
* ** &lt;Reproduzir&gt;**: é reproduzido um ficheiro de áudio.
* ** &lt;Pausa&gt;**: aguarda silenciosamente durante um número especificado de segundos.
* ** &lt;Registo&gt;**: regista voice o autor da chamada e devolve um URL de um ficheiro que contém a gravação.
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

## <a id="create_app"></a>Criar uma aplicação de Java
1. Obtenha o Twilio JAR e adicioná-lo ao seu caminho de compilação de Java e a assemblagem de implementação de guerra. Em [https://github.com/twilio/twilio-java][twilio_java], pode transferir as origens de GitHub e criar o seu próprio para a caixa ou transferir uma previamente concebida para caixa (com ou sem dependências).
2. Certifique-se keystore do seu JDK **cacerts** contém o certificado de Equifax seguro autoridade de certificação com MD5 identificação 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a identificação de SHA1 é D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este é o certificado de certificação (AC) certificado para o [https://api.twilio.com] [ twilio_api_service] serviço, que chama-se ao utilizar Twilio APIs. Para obter informações sobre garantindo que seu JDK **cacerts** keystore contém o certificado de AC correto, consulte o artigo [Adicionar um certificado para o arquivo de certificados de AC Java][add_ca_cert].

Instruções detalhadas sobre como utilizar a biblioteca do cliente Twilio para Java está disponíveis no [como fazer Twilio de utilizar uma chamada telefónica numa aplicação Java no Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurar a aplicação para utilizar bibliotecas de Twilio
No seu código, pode adicionar **Importar** declarações na parte superior dos seus ficheiros de origem para o Twilio pacotes ou classes que pretende utilizar na sua aplicação. 

Para ficheiros de origem Java:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Para ficheiros de origem de página de servidor de Java (JSP):

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
Dependendo de qual Twilio pacotes ou as classes que pretende utilizar, os extratos **Importar** podem ser diferentes.

## <a id="howto_make_call"></a>Como: efetuar uma chamada de saída
A imagem seguinte mostra como efetuar uma chamada de saída utilizando a classe de **CallFactory** . Este código utiliza também um site Twilio fornecido para devolver a resposta Twilio Markup Language (TwiML). Substitua os valores para os números de telefone **de** e **até** e certifique-se de que verifique se o número de telefone **a partir** da sua conta Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Para obter mais informações acerca dos parâmetros transmitido para o método de **CallFactory.create** , consulte o artigo [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Tal como mencionado, este código utiliza um site de Twilio fornecido para devolver a resposta TwiML. Em vez disso, pode utilizar o seu próprio site para fornecer a resposta TwiML; Para mais informações, consulte o artigo [como fornecer TwiML as respostas numa aplicação Java no Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: enviar uma mensagem SMS
A imagem seguinte mostra como enviar uma mensagem SMS utilizando a classe de **SmsFactory** . **A partir do** número, **4155992671**, é fornecido pela Twilio para contas de avaliação para enviar mensagens SMS. **O número** tem de ser validado para a sua conta Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Para obter mais informações acerca dos parâmetros transmitido para o método de **SmsFactory.create** , consulte o artigo [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Como: fornecer respostas TwiML do seu próprio Web site
Quando a aplicação inicia uma chamada à Twilio API, por exemplo através do método de **CallFactory.create** Twilio irá enviar o seu pedido para um URL que esperado para devolver uma resposta TwiML. O exemplo acima utiliza o URL Twilio fornecido pela [http://twimlets.com/message][twimlet_message_url]. (Enquanto TwiML foi concebido para utilização por serviços Web, pode ver o TwiML no seu browser. Por exemplo, clique em [http://twimlets.com/message] [ twimlet_message_url] para ver uma esvaziar ** &lt;resposta&gt; ** elemento; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] para ver uma ** &lt;resposta&gt; ** elemento que contém uma ** &lt;diga&gt; ** elemento.)

Em vez de depender do URL Twilio fornecido pela, pode criar o seu próprio site de URL devolve respostas HTTP. Pode criar o site de qualquer idioma que devolve as respostas de HTTP; Este tópico pressupõe que vai ser alojamento o URL de uma página JSP.

Página JSP seguinte resulta no envio uma resposta TwiML que diz **Olá mundo** na chamada.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

Página JSP seguinte resulta no envio uma resposta TwiML que diz algum texto, tem várias pausas e diz informações sobre a versão da Twilio API e o nome da função Azure.


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

O parâmetro **ApiVersion** está disponível nos pedidos de voz Twilio (não pedidos SMS). Para ver os parâmetros de pedido disponíveis para pedidos SMS e Twilio voz, consulte o artigo <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respetivamente. A variável de ambiente de **nome da função** está disponível como parte de uma implementação do Azure. (Se quiser adicionar variáveis de ambiente personalizado, de modo que poderiam selecionados para cima a partir do **System.getenv**, consulte a secção de variáveis de ambiente no [Diversos definições de configuração de função][misc_role_config_settings].)

Assim que tiver a sua página JSP definida para fornecer respostas TwiML, utilize o URL da página JSP como o URL passado para o método de **CallFactory.create** . Por exemplo, se tiver uma aplicação Web com o nome MyTwiML implementado um Azure alojado serviço e o nome da página JSP é mytwiml.jsp, o URL pode ser passado para **CallFactory.create** conforme mostrado no seguinte:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Outra opção para responder com TwiML é através da classe **TwiMLResponse** , que se encontra disponível no pacote **com.twilio.sdk.verbs** .

Para obter informações adicionais sobre como utilizar Twilio no Azure com Java, consulte o artigo [como fazer Twilio de utilizar uma chamada telefónica numa aplicação Java no Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Como: utilizar os serviços de Twilio adicionais
Para além dos exemplos apresentados aqui, Twilio oferece APIs baseada na web que pode utilizar para tirar partido de funcionalidades Twilio adicionais a partir da sua aplicação Azure. Para detalhes completos, consulte a [documentação da Twilio API] [twilio_api_documentation].

## <a id="NextSteps"></a>Próximos passos
Agora que aprendeu os princípios básicos para o serviço de Twilio, siga estas ligações para mais informações:

* [Diretrizes de segurança Twilio] [twilio_security_guidelines]
* [Twilio How to e o código de exemplo] [twilio_howtos]
* [Tutoriais Twilio guia de introdução][twilio_quickstarts] 
* [Twilio no GitHub] [twilio_on_github]
* [Falar com suporte de Twilio] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
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
