<properties 
    pageTitle="Como fazer um chamada telefónica do Twilio (Java) | Microsoft Azure" 
    description="Saiba como fazer um chamada telefónica a partir de uma página web utilizando Twilio numa aplicação Java no Azure." 
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

# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Como fazer um chamada telefónica utilizando Twilio numa aplicação Java no Azure 

O exemplo seguinte mostra-lhe como pode utilizar Twilio para efetuar uma chamada de uma página web alojado no Azure. A aplicação resultante irá pedir ao utilizador para valores de chamada telefónica, conforme apresentado na seguinte captura de ecrã.

![Formulário de chamada Azure utilizando Twilio e Java][twilio_java]

Terá de fazer o seguinte para utilizar o código neste tópico:

1. Adquirir uma conta de Twilio e autenticação token. Para começar com Twilio, avaliar preços na [http://www.twilio.com/pricing][twilio_pricing]. Pode inscrever-se na [https://www.twilio.com/try-twilio][try_twilio]. Para obter informações sobre a API fornecida pela Twilio, consulte o artigo [http://www.twilio.com/api][twilio_api].
2. Obter o Twilio para caixa. Em [https://github.com/twilio/twilio-java][twilio_java_github], pode transferir as origens de GitHub e criar o seu próprio para a caixa ou transferir uma previamente concebida para caixa (com ou sem dependências).
O código neste tópico foi escrito utilizando a previamente concebida para TwilioJava 3.3.8 com dependências caixa.
3. Adicione para a caixa ao seu caminho de compilação Java.
4. Se estiver a utilizar Eclipse para criar esta aplicação Java, inclua o Twilio JAR no seu ficheiro de implementação de aplicação (guerra) utilizando a funcionalidade de assemblagem de implementação do Eclipse. Se não estiver a utilizar Eclipse para criar esta aplicação Java, certifique-se de que o Twilio JAR é incluído no prazo da mesma função Azure como a sua aplicação Java e adicionado ao caminho de classe da sua aplicação.
5. Certifique-se de que seu keystore cacerts contém o certificado de Equifax seguro autoridade de certificação com MD5 identificação 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a identificação de SHA1 é D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este é o certificado de certificação (AC) certificado para o [https://api.twilio.com] [ twilio_api_service] serviço, que chama-se ao utilizar Twilio APIs. Para obter informações sobre como adicionar este certificado AC a loja do seu JDK cacert, consulte o artigo [Adicionar um certificado para o arquivo de certificados Java AC][add_ca_cert].

Para além disso, familiaridade com as informações na [criação de um Olá mundo aplicação utilizando o Toolkit de Azure para Eclipse][azure_java_eclipse_hello_world], ou com outras técnicas para alojar aplicações Java no Azure se não estiver a utilizar Eclipse, é vivamente recomendado.

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário web para fazer uma chamada

O código seguinte mostra como criar um formulário web para obter dados de utilizador para fazer uma chamada. Para fins neste exemplo, um novo projeto de web dinâmico, com o nome **TwilioCloud**, foi criado e **callform.jsp** foi adicionado como um ficheiro JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Criar o código para efetuar uma chamada
O código seguinte, o que é chamado quando o utilizador preenche o formulário apresentado ao callform.jsp, cria a mensagem chamada e gera a chamada. Para fins neste exemplo, o ficheiro JSP com o nome **makecall.jsp** e foi adicionado ao projeto **TwilioCloud** . (Utilize a conta do Twilio e autenticação token em vez dos valores de marcador de posição atribuídos ao **accountSID** e **authToken** no código abaixo).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Para além de fazer a chamada, makecall.jsp apresenta o ponto final Twilio, versão API e o estado de chamada. Um exemplo é a seguinte captura de ecrã:

![Resposta de chamada Azure utilizando Twilio e Java][twilio_java_response]

## <a name="run-the-application"></a>Executar a aplicação
Seguem-se os passos de alto nível para executar a sua aplicação; detalhes para estes passos podem ser encontrados em [criar um Olá mundo aplicação utilizando o Toolkit de Azure para Eclipse][azure_java_eclipse_hello_world].

1. Exporte o guerra TwilioCloud para a pasta Azure **approot** . 
2. Modificar **startup.cmd** deszipar o seu guerra TwilioCloud.
3. A aplicação para o emulador cluster de compilação.
4. Inicie a sua implementação no emulador de cluster.
5. Abra um browser e executar **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Introduza os valores no formulário, clique em **tornar esta chamada**e, em seguida, veja os resultados na makecall.jsp.

Quando estiver pronto para implementar Azure, recompilação para implementação na nuvem, implementar para Azure e executar http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp no browser (substituir o valor para *your_hosted_name*).

## <a name="next-steps"></a>Próximos passos
Este código foi fornecido para confirmar que a funcionalidade básica com Twilio em Java no Azure. Antes de implementar para Azure na produção, poderá querer adicionar mais processamento de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar blobs do Azure armazenamento ou base de dados SQL para armazenar números de telefone e chamada de texto. Para obter informações sobre a utilização de blobs do Azure armazenamento em Java, veja [como utilizar o serviço de armazenamento de Blobs do Java][howto_blob_storage_java]. Para obter informações sobre como utilizar a base de dados do SQL no Java, consulte o artigo [Utilizar base de dados SQL em Java][howto_sql_azure_java].
* Pode utilizar **RoleEnvironment.getConfigurationSettings** para obter o ID da conta Twilio e token de autenticação a partir das definições de configuração da sua implementação, em vez dos valores na makecall.jsp de disco rígido codificação. Para obter informações sobre a classe de **RoleEnvironment** , consulte o artigo [utilizar a biblioteca de Runtime Azure Service no JSP] [ azure_runtime_jsp] e a documentação de pacote Azure Service Runtime em [http://dl.windowsazure.com/javadoc][azure_javadoc].
* O código de makecall.jsp atribui um URL Twilio fornecido pela, [http://twimlets.com/message][twimlet_message_url], para a variável de **Url** . Este URL fornece uma resposta Twilio Markup Language (TwiML) que informa Twilio como continuar a chamada. Por exemplo, o que é devolvido TwiML pode conter um ** &lt;diga&gt; ** verbais que resulta em texto falado ao destinatário da chamada. Em vez de utilizar o URL Twilio fornecido pela, poderia construir a sua própria serviço para responder a pedido do Twilio; Para mais informações, consulte o artigo [como utilizar Twilio para voz e capacidades de SMS no Java][howto_twilio_voice_sms_java]. Obter mais informações sobre TwiML podem ser encontradas em [http://www.twilio.com/docs/api/twiml][twiml]e obter mais informações sobre ** &lt;diga&gt; ** e outros verbos Twilio podem ser encontrados em [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leia as diretrizes de segurança Twilio na [https://www.twilio.com/docs/security][twilio_docs_security].

Para obter informações adicionais sobre Twilio, consulte o artigo [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Consulte também
* [Como utilizar Twilio para voz e capacidades SMS no Java][howto_twilio_voice_sms_java]
* [Adicionar um certificado para o arquivo de certificados de AC Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
