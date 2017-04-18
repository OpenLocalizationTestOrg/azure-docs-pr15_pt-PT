<properties 
    pageTitle="Como utilizar o serviço de correio electrónico SendGrid (.NET) | Microsoft Azure" 
    description="Saiba como enviar e-mail com o serviço de e-mail SendGrid no Azure. O código de amostras escritas c# e utilizar a API do .NET." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="thinkingserious" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/14/2016" 
    ms.author="team-pi@sendgrid.com"/>





# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Como enviar correio eletrónico utilizando SendGrid com Azure


## <a name="overview"></a>Descrição geral

Este guia demonstra como efetuar tarefas comuns de programação com o serviço de e-mail SendGrid no Azure. Os exemplos estão escritos em C\#
e utilizar a API do .NET. Os cenários abrangidos incluam **construir o e-mail**, **Enviar e-mail**, **Adicionar anexos**e **através dos filtros**. Para mais informações sobre SendGrid e envio de e-mail, consulte a secção [os passos seguintes][] .

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de e-mail SendGrid?

SendGrid é um [serviço de e-mail baseada na nuvem] que fornece fiável [entrega de e-mail transaccionais], escalabilidade e em tempo real analytics juntamente com APIs flexíveis que facilitam a integração personalizada. Cenários comuns de utilização de SendGrid incluem:

-   Enviar automaticamente recibos aos clientes.
-   Administrar distribuição listas para o envio de clientes e-fliers mensais e ofertas especiais.
-   Recolher métricas em tempo real para coisas como bloqueado e-mail e a capacidade de resposta do cliente.
-   Gerar relatórios para ajudar a identificar tendências.
-   Inquéritos do cliente de reencaminhamento de chamadas.
-   Processamento de receber e-mails.

Para mais informações, consulte o artigo [https://sendgrid.com](https://sendgrid.com) ou nosso [c# biblioteca][sendgrid csharp]

## <a name="create-a-sendgrid-account"></a>Criar uma conta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referência a biblioteca de classes SendGrid .NET

O [pacote de SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) é a forma mais fácil para obter a API SendGrid e para configurar a aplicação com todas as dependências. NuGet é uma extensão do Visual Studio incluída com o Microsoft Visual Studio 2015 que torna mais fácil instalar e actualizar bibliotecas e ferramentas. 

> [AZURE.NOTE] Para instalar NuGet se estiver a executar uma versão anterior ao Visual Studio 2015 do Visual Studio, visite [http://www.nuget.org](http://www.nuget.org)e clique no botão **Instalar NuGet** .

Para instalar o pacote de SendGrid NuGet na sua aplicação, faça o seguinte:

1.  Criar um novo projeto.

    ![Criar um novo projeto][create-new-project]

2.  Selecione um modelo.

    ![Selecionar um modelo][select-a-template]

3.  No **Explorador de soluções**, **referências**de contexto, em seguida, clique em **Gerir pacotes de NuGet**.

4.  Procurar **SendGrid** e selecione o item de **SendGrid** na lista de resultados.

    ![Pacote SendGrid NuGet][SendGrid-NuGet-package]

5.  Clique em **instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.

Biblioteca de classes .NET do SendGrid chama **SendGridMail**. Contém os espaços de nomes seguintes:

-   **SendGridMail** para criar e trabalhar com itens de correio eletrónico.
-   **SendGridMail.Transport** para o envio de correio eletrónico utilizando o protocolo **SMTP** ou protocolo HTTP 1.1 com **Web/restantes**.

Adicione as declarações de espaço de nomes de código seguintes para a parte superior de qualquer C\# ficheiro na qual pretende através de programação aceder ao serviço de e-mail de SendGrid.
**System.Net** e **System.Net.Mail** são .NET Framework espaços de nomes que estão incluídos porque incluem tipos que normalmente irá utilizar com a API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Como: criar uma mensagem de e-mail

Utilize o objeto **SendGridMessage** para criar uma mensagem de e-mail. Assim que o objeto de mensagem for criado, pode definir propriedades e métodos, incluindo o remetente de correio eletrónico, o destinatário de correio eletrónico e o assunto e o corpo do correio eletrónico.

O exemplo seguinte demonstra como criar um objeto de correio eletrónico completamente preenchida:

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Para mais informações sobre todas as propriedades e métodos de suportados pelo tipo **SendGrid** , consulte o artigo [sendgrid csharp][] GitHub.

## <a name="how-to-send-an-email"></a>Como: enviar uma mensagem de e-mail

Depois de criar uma mensagem de e-mail, pode enviar-utilizar a API Web fornecida pela SendGrid. Em alternativa, pode utilizar [. Líquido incorporado na biblioteca](https://sendgrid.com/docs/Code_Examples/csharp.html).

Enviar e-mail requer que forneceu credenciais da sua SendGrid conta (nome de utilizador e palavra-passe) ou SendGrid API chave. API chave é o método preferencial. Se precisar de mais informações sobre como configurar as teclas de API, visite a nossa [documentação](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

Pode armazenar estas credenciais através do seu portal do Azure ao clicar em configurar e adicione os pares valor/chave em "as definições da aplicação".

 ![Definições da aplicação Azure][azure_app_settings]

 Em seguida, poderá acedê-los da seguinte forma: 
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Com as credenciais:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Utilizar a API chave:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


Os exemplos seguintes mostram como enviar uma mensagem com a API Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Como: adicionar um anexo

Anexos podem ser adicionados a uma mensagem chamar o método de **AddAttachment** e especificando o nome e o caminho do ficheiro que pretende anexar.
Pode incluir vários anexos ao contactar o suporte este método uma vez para cada ficheiro que pretende anexar. O exemplo seguinte demonstra a adicionar um anexo a uma mensagem:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Também pode adicionar anexos a partir dos dados **sequência**. Puder ser processada ao contactar o suporte ao mesmo método acima, **AddAttachment**, mas por passagem na sequência dos dados e o nome de ficheiro que pretende mostrar tal como a mensagem. Neste caso terá de adicionar a biblioteca de System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Como: utilizar as aplicações para activar rodapés, rastreio e análise

SendGrid fornece uma funcionalidade de e-mail adicionais através da utilização de aplicações. Estas são as que podem ser adicionados a uma mensagem de correio eletrónico para ativar a funcionalidade específica como clique em controlo, Google analytics, subscrição rastreio, e assim sucessivamente. Para obter uma lista completa de aplicações, consulte o artigo [Definições de aplicação][].

Aplicações podem ser aplicadas às mensagens de e-mail **SendGrid** através de métodos implementados como parte da classe **SendGrid** .

Os exemplos seguintes demonstram o rodapé e clique em filtros de registo:

### <a name="footer"></a>Rodapé

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Clique em controlo

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Como: utilizar os serviços de SendGrid adicionais

SendGrid oferece APIs e webhooks que pode utilizar para tirar partido de funcionalidades SendGrid adicionais a partir da sua aplicação Azure baseada na web. Para detalhes completos, consulte a [documentação da SendGrid API][].

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para o serviço de E-Mail SendGrid, siga estas ligações para mais informações.

*   SendGrid C\# repo biblioteca: [sendgrid csharp][]
*   Documentação da SendGrid API: <https://sendgrid.com/docs>
*   SendGrid ofertas especiais para os clientes do Azure: [https://sendgrid.com](https://sendgrid.com)

  [Próximos passos]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Definições da aplicação]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentação da SendGrid API]: https://sendgrid.com/docs
  
  [serviço de e-mail baseada na nuvem]: https://sendgrid.com/email-solutions
  [entrega de e-mail transaccionais]: https://sendgrid.com/transactional-email
 
