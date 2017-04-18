<properties 
    pageTitle="Como fazer um chamada telefónica do Twilio (.NET) | Microsoft Azure" 
    description="Saiba como efetuar uma chamada e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos .NET." 
    services="" 
    documentationCenter=".net" 
    authors="devinrader" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="05/04/2016" 
    ms.author="microsofthelp@twilio.com"/>




# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Como fazer um chamada telefónica utilizando Twilio numa função de web no Azure

Este guia demonstra como utilizar Twilio para efetuar uma chamada a partir de uma página web alojada no Azure. A aplicação resultante pede ao utilizador para valores de chamada telefónica, conforme apresentado na captura de ecrã seguinte.

![Formulário de chamada Azure utilizando Twilio e ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Pré-requisitos

Terá de fazer o seguinte para utilizar o código neste tópico:

1. Adquirir uma conta de Twilio e autenticação token. Para começar com Twilio, inscrever-se na [https://www.twilio.com/try-twilio][try_twilio]. Pode avaliar preços na [http://www.twilio.com/pricing][twilio_pricing]. Para obter informações sobre a API fornecida pela Twilio, consulte o artigo [http://www.twilio.com/voice/api][twilio_api].
2. Adicione a biblioteca de Twilio .NET à sua função web. Consulte o artigo "para adicionar as bibliotecas Twilio ao seu projeto de funções da web," mais adiante.

Deve estar familiarizado com a criação de uma função de web básicas no Azure.

## <a name="howtocreateform"></a>Como: criar um formulário web para fazer uma chamada

<a id="use_nuget"></a>Para adicionar as bibliotecas Twilio ao seu projeto de funções da web:

1.  Abra a sua solução no Visual Studio.
2.  Botão direito do rato **referências**.
3.  Clique em **Gerir NuGet pacotes**.
4.  Clique em **Online**.
5.  Na caixa de pesquisa online, escreva *twilio*.
6.  Clique em **instalar** o pacote de Twilio.

O código seguinte mostra como criar um formulário web para obter dados de utilizador para fazer uma chamada. Neste exemplo, uma função de web do ASP.NET denominada **TwilioCloud** é criada.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <a id="howtocreatecode"></a>Como: criar o código para efetuar uma chamada
O código seguinte, o que é chamado quando o utilizador preenche o formulário, cria a mensagem chamada e gera a chamada. Neste exemplo, o código é executado no processador de eventos AoFazerClique do botão no formulário. (Utilize a conta do Twilio e autenticação token em vez dos valores de marcador de posição atribuídos ao **accountSID** e **authToken** no código abaixo).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

A chamada é efetuada e o ponto final Twilio, versão API e o estado de chamada são apresentados. A seguinte captura de ecrã mostra a saída de executar um exemplo.

![Resposta chamada Azure utilizando Twilio e ASP.NET][twilio_dotnet_basic_form_output]

Obter mais informações sobre TwiML podem ser encontradas em [http://www.twilio.com/docs/api/twiml][twiml]. Obter mais informações sobre &lt;diga&gt; e outros verbos Twilio podem ser encontrados em [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Próximos passos
Este código foi fornecido para confirmar que a funcionalidade básica utilizando Twilio numa função de web do ASP.NET no Azure. Antes de implementar para Azure na produção, poderá querer adicionar mais processamento de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar armazenamento de Blobs do Azure ou uma instância de base de dados do Azure SQL para armazenar números de telefone e chamada de texto. Para obter informações sobre como utilizar blobs no Azure, veja [como utilizar o serviço de armazenamento de Blobs do Azure no .NET][howto_blob_storage_dotnet]. Para obter informações sobre como utilizar a base de dados SQL, veja [como utilizar a base de dados do SQL Azure em aplicações .NET][howto_sql_azure_dotnet].
* Pode utilizar RoleEnvironment.getConfigurationSettings para obter o ID da conta Twilio e token de autenticação a partir das definições de configuração da sua implementação, em vez dos valores no formulário de disco rígido codificação. Para obter informações sobre a classe de RoleEnvironment, consulte o artigo [Microsoft.WindowsAzure.ServiceRuntime espaço de nomes][azure_runtime_ref_dotnet].
* Leia as diretrizes de segurança Twilio na [https://www.twilio.com/docs/security][twilio_docs_security].
* Mais informações sobre Twilio na [https://www.twilio.com/docs][twilio_docs].

##<a name="seealso"></a>Consulte também
* [Como utilizar Twilio para voz e capacidades SMS a partir do Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
