<properties 
    pageTitle="Store-sendgrid-Java-How-to-send-email-example" 
    description="Como enviar correio eletrónico utilizando SendGrid a partir do Java numa implementação do Azure" 
    services="" 
    documentationCenter="java" 
    authors="thinkingserious" 
    manager="sendgrid" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com"/>

# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Como enviar correio eletrónico utilizando SendGrid a partir do Java numa implementação do Azure

O exemplo seguinte mostra-lhe como pode utilizar SendGrid para enviar e-mails a partir de uma página web alojada no Azure. A aplicação resultante irá pedir ao utilizador para valores de correio eletrónico, conforme apresentado na seguinte captura de ecrã.

![Formulário de correio eletrónico][emailform]

O e-mail resultante terá uma aspeto semelhante ao seguinte captura de ecrã.

![Mensagem de correio eletrónico][emailsent]

Terá de fazer o seguinte para utilizar o código neste tópico:

1. Obter jarros javax.mail, por exemplo a partir do <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Adicione os jarros ao seu caminho de compilação Java.
3. Se estiver a utilizar Eclipse para criar esta aplicação Java, pode incluir as bibliotecas SendGrid no seu ficheiro de implementação de aplicação (guerra) utilizando a funcionalidade de assemblagem de implementação do Eclipse. Se não estiver a utilizar Eclipse para criar esta aplicação Java, certifique-se de que as bibliotecas estão incluídas no prazo da mesma função Azure como a sua aplicação Java e adicionadas ao caminho de classe da sua aplicação.


Também tem de ter suas próprias SendGrid nome de utilizador e palavra-passe, para conseguir envie o e-mail. Para começar com SendGrid, consulte o artigo [como enviar correio eletrónico utilizando SendGrid a partir do Java](store-sendgrid-java-how-to-send-email.md).

Para além disso, familiaridade com as informações na [criação de uma aplicação de Olá mundo para Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944)ou em conjunto com outras técnicas para alojar aplicações Java no Azure se não estiver a utilizar Eclipse, é vivamente recomendada.

## <a name="create-a-web-form-for-sending-email"></a>Criar um formulário web para o envio de correio eletrónico

O código seguinte mostra como criar um formulário web para obter dados de utilizador para o envio de correio eletrónico. Para fins deste conteúdo, o ficheiro JSP é denominado **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Criar o código para enviar a mensagem de correio electrónico

O código seguinte, o que é denominado quando concluir o formulário na emailform.jsp, cria a mensagem de e-mail e envia-lo. Para fins deste conteúdo, o ficheiro JSP é denominado **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%
     
     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");
     
     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;
          
            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {
         
         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";
    
         Properties properties;
        
         properties = new Properties();
         
         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");
         
         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
    
         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();
         
         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);
         
         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);
    
         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 
         
         message = new MimeMessage(mailSession);
         
         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            
    
         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);
         
         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
    
         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
         
         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();
     
        out.println("<p>Email processing completed.</p>");
         
     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>
    
    </body>
    </html>

Para além de enviar o e-mail, emailform.jsp fornece um resultado para o utilizador; um exemplo é a seguinte captura de ecrã:

![Enviar o resultado de correio][emailresult]

## <a name="next-steps"></a>Próximos passos

Implementar a aplicação para o emulador cluster e dentro de um browser executar emailform.jsp, introduza os valores no formulário, clique em **Enviar esta mensagem de correio electrónico**e, em seguida, consulte o artigo resultados na sendemail.jsp.

Este código foi fornecido para mostrar-lhe como utilizar SendGrid em Java no Azure. Antes de implementar para Azure na produção, poderá querer adicionar mais processamento de erros ou outras funcionalidades. Por exemplo: 

* Pode utilizar blobs do Azure armazenamento ou base de dados SQL para armazenar os endereços de e-mail e mensagens de correio eletrónico, em vez de utilizar um formulário web. Para obter informações sobre a utilização de blobs do Azure armazenamento em Java, consulte o artigo [como utilizar o serviço de armazenamento de Blobs do Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Para obter informações sobre como utilizar a base de dados do SQL no Java, consulte o artigo [Utilizar base de dados SQL em Java](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* É possível utilizar `RoleEnvironment.getConfigurationSettings` para obter SendGrid nome de utilizador e palavra-passe a partir das definições de configuração da sua implementação, em vez de utilizar o formulário na web para obter esses valores. Para obter informações sobre o `RoleEnvironment` classe, consulte o artigo [utilizar a biblioteca de Runtime Azure Service no JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) e a documentação de pacote Azure Service Runtime em <http://dl.windowsazure.com/javadoc>.
* Para mais informações sobre como utilizar SendGrid em Java, consulte o artigo [como enviar correio eletrónico utilizando SendGrid a partir do Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
