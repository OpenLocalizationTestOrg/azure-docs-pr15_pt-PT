<properties 
    pageTitle="Como configurar autenticação comum TLS para o Web App" 
    description="Saiba como configurar a sua aplicação web utilizar autenticação de certificado de cliente no TLS." 
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/08/2016" 
    ms.author="naziml"/>    

# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Como configurar autenticação comum TLS para o Web App

## <a name="overview"></a>Descrição geral ##
Pode restringir o acesso a sua aplicação Azure web ao ativar diferentes tipos de autenticação para a mesma. É uma forma de fazê-lo autenticar a utilizar um certificado de cliente, quando o pedido for através de TLS/SSL. Este mecanismo é chamado autenticação comum TLS ou certificado de cliente de autenticação e este artigo irão de detalhe como configurar a sua aplicação web para utilizar a autenticação de certificado de cliente.

> **Nota:** Se aceder ao seu site HTTP e HTTPS não, não receberá qualquer certificado de cliente. Por isso, se a sua aplicação requer certificados de cliente não deve permitir os pedidos para a sua aplicação através de HTTP.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="configure-web-app-for-client-certificate-authentication"></a>Configurar o Web App para a autenticação de certificado de cliente ##
Configurar o seu web app para requerer certificados de cliente tem de adicionar a definição do site clientCertEnabled para a sua aplicação web e defini-lo como verdadeiro. Esta definição não está atualmente disponível através a experiência de gestão no Portal do e os REST API terá de ser utilizados para realizar esta tarefa.

Pode utilizar a [ferramenta de ARMClient](https://github.com/projectkudu/ARMClient) para tornam mais fácil crie a chamada REST API. Depois de iniciar sessão com a ferramenta de terá o seguinte comando de problema:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose
    
Substituir tudo num {} com informações para a sua aplicação web e criar um ficheiro denominado enableclientcert.json com o JSON seguinte conteúdo:

> {"localização": "A minha Web App localização",   
>   "Propriedades": {  
>     "clientCertEnabled": VERDADEIRO}}  

Certifique-se alterar o valor de "localização" para o local onde se encontra a aplicação web, por exemplo, o América do Norte Central-nos ou oeste EUA etc.

> **Nota:** Se executar ARMClient a partir do Powershell, terá de escape a @ símbolo para o ficheiro JSON com uma escala de trás '.

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Aceder ao certificado de cliente da sua aplicação Web ##
Se estiver a utilizar o ASP.NET e configurar a sua aplicação para utilizar a autenticação de certificado de cliente, o certificado estarão disponível através da propriedade **HttpRequest.ClientCertificate** . Para outras pilhas de aplicação, o certificado de cliente estarão disponível na sua aplicação através de um valor de codificação base64 no cabeçalho do pedido de "X-ARR ClientCert". A aplicação pode criar um certificado deste valor e, em seguida, utilizá-la para fins de autenticação e autorização na sua aplicação.

## <a name="special-considerations-for-certificate-validation"></a>Considerações especiais para a validação do certificado ##
O certificado de cliente é enviado para a aplicação não percorrer qualquer validação pela plataforma do Azure Web Apps. Validar este certificado é da responsabilidade da aplicação web. Aqui é o código ASP.NET de exemplo que valida propriedades de certificado para fins de autenticação.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;
                
                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;
                
                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
