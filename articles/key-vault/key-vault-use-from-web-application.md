<properties
    pageTitle="Utilizar cofre chave Azure a partir de uma aplicação Web | Microsoft Azure"
    description="Utilize este tutorial para ajudá-lo a aprender a utilizar o Azure chave cofre a partir de uma aplicação web."
    services="key-vault"
    documentationCenter=""
    authors="adhurwit"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-azure-key-vault-from-a-web-application"></a>Utilizar cofre chave Azure a partir de uma aplicação Web #

## <a name="introduction"></a>Introdução  
Utilize este tutorial para ajudá-lo a aprender a utilizar o Azure chave cofre a partir de uma aplicação web no Azure. -Orienta-pelo processo de aceder a uma palavra-passe do Cofre de chave Azure para que possa ser utilizado na sua aplicação web.

**Estimativa tempo a concluir:** 15 minutos


Para obter informações gerais sobre Azure chave cofre, consulte o artigo [o que é o Azure chave cofre?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter o seguinte procedimento:

- Um URI para uma palavra-passe num cofre de chave do Azure
- Um ID de cliente e uma palavra-passe cliente para uma aplicação web registarem com o Azure Active Directory que tenha acesso ao seu Cofre chave
- Uma aplicação web. Vamos será mostrada os passos para uma aplicação do ASP.NET MVC implementado no Azure como uma aplicação Web.

> [AZURE.NOTE]  É essencial que concluiu os passos indicados no [Artigo Introdução ao Azure chave cofre](key-vault-get-started.md) para este tutorial, para que tenha o URI a uma palavra-passe e o ID de cliente e o cliente segredo para uma aplicação web.

A aplicação web que terão acesso ao Cofre de palavras chave é aquele que é registada no Azure Active Directory e tenha sido concedido acesso Cofre de chave. Se não for o caso, voltar atrás para registar uma aplicação no tutorial começar e repita os passos listados.

Neste tutorial foi concebido para os programadores de web compreender as noções básicas da criação de aplicações web no Azure. Para mais informações sobre o Azure Web Apps, consulte o artigo [Descrição geral do Web Apps](../app-service-web/app-service-web-overview.md).



## <a id="packages"></a>Adicionar pacotes Nuget ##
Existem dois pacotes que necessita de ter instalado a aplicação web.

- Biblioteca de autenticação do Active Directory - contém métodos para interagir com o Azure Active Directory e gerir a identidade do utilizador
- Biblioteca do cofre chave Azure - contém métodos para interagir com Cofre de chave do Azure


Ambos estes pacotes podem ser instalados utilizando a consola do Gestor de pacote utilizando o comando do pacote de instalação.

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a id="webconfig"></a>Modificar a Web. config ##
Existem três definições da aplicação que precisam de ser adicionadas ao ficheiro Web. config da seguinte forma.

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Se não passar para alojar o seu pedido como uma aplicação Web do Azure, deve adicionar os valores reais de ClientId, segredo cliente e URI de palavra-passe para a Web. config. Caso contrário, deixe estes valores fictícios porque podemos irá adicionar os valores reais no Portal do Azure para um nível de segurança adicional.


## <a id="gettoken"></a>Adicionar método para obter um acesso Token ##
Para que possa utilizar a API do cofre chave tem um token de acesso. O cliente do cofre chave processa chamadas para a API do cofre chave mas é necessário fornecer-lhe uma função que obtém o token de acesso.  

Segue-se o código para obter um acesso tokens a partir do Azure Active Directory. Este código pode aceder a qualquer local na sua aplicação. Pretende adicionar uma classe utilitários ou EncryptionHelper.  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Threading.Tasks;
    using System.Web.Configuration;

    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public static async Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

> [AZURE.NOTE] 
> Utilizar um ID de cliente e o segredo cliente é a forma mais fácil para autenticar uma aplicação do Azure AD. E utilizá-lo na sua aplicação web permite uma separação de funções e maior controlo sobre a gestão de chave. Mas depender de colocação do segredo cliente as definições de configuração que, para que algumas, podem ser tão arriscadas como colocar o segredo que pretende proteger nas suas definições de configuração. Consulte abaixo para mais informações sobre como utilizar um ID de cliente e o certificado em vez de ID de cliente e o segredo do cliente para autenticar a aplicação do Azure AD.



## <a id="appstart"></a>Obter o segredo no iniciar aplicação ##
Agora, é necessário código para ligar para a API do cofre chave e obter o segredo. O código seguinte pode ser colocado em qualquer lugar como chama-se antes de necessitar utilizá-la. Ter colocar este código no evento iniciar aplicação na asax para que é executada uma vez no início e disponibiliza o segredo para a aplicação.

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Adicionar as definições da aplicação no Portal do Azure (opcional) ##
Se tiver uma aplicação Web do Azure agora pode adicionar os valores reais para AppSettings no Portal do Azure. Ao fazê-lo, os valores reais não serão na Web. config mas protegido através do Portal do onde tem funcionalidades de controlo de acesso separado. Estes valores vai ser substituídos para os valores que tenha introduzido no seu Web. config. Certifique-se de que os nomes são os mesmos.

![Definições da aplicação apresentadas no Portal do Azure][1]


## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autenticar com um certificado em vez de uma palavra-passe cliente
Outra forma para autenticar uma aplicação do Azure AD é utilizando um ID de cliente e um certificado em vez de um ID de cliente e o segredo cliente. Seguem-se os passos para utilizar um certificado numa aplicação Web do Azure:

1. Obter ou criar um certificado
2. Associar o certificado de uma aplicação do Azure AD
3. Adicionar código para a sua aplicação Web para utilizar o certificado
4. Adicionar um certificado para a sua aplicação Web


**Obter ou criar um certificado** Para as nossas finalidades oferecemos um certificado de teste. Aqui estão algumas comandos que pode utilizar numa linha de comandos para programadores para criar um certificado. Alterar directório para onde pretende que os ficheiros de orientação do diapositivo notas seja criado.

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Tome nota da data de fim e a palavra-passe para o. pfx (neste exemplo: 31/07/2016 e test123). Terá de lhes abaixo.

Para obter mais informações sobre como criar um certificado de teste, consulte o artigo [como: criar o próprio testar certificado](https://msdn.microsoft.com/library/ff699202.aspx)


**Associar o certificado com uma aplicação do Azure AD** Agora que tem um certificado, é necessário associá-lo a uma aplicação do Azure AD. Mas o Portal de gestão do Azure não suporta esta neste momento. Em vez disso, tem de utilizar o Powershell. Seguem-se os comandos que precisa para executar:

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

    PS C:\> $x509.Import("C:\data\KVWebApp.cer")

    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    PS C:\> $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

Depois de executar estes comandos, pode ver a aplicação no Azure AD. Se não vir a aplicação em primeiro lugar, procure "Aplicações proprietário da minha empresa" em vez de "A minha empresa utiliza aplicações".

Para saber mais sobre objectos de aplicação do Azure AD e ServicePrincipal, consulte o artigo [objectos da aplicação e objetos do Principal do serviço](../active-directory/active-directory-application-objects.md)



**Adicionar código para a sua aplicação Web para utilizar o certificado** Vamos agora adicionar código para a sua aplicação Web para aceder a orientação do diapositivo notas e utilizá-lo para a autenticação.

Primeiro é código para aceder a orientação do diapositivo notas.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Note que StoreLocation é UtilizadorActual em vez de LocalMachine. E que recomendamos são fornecer as 'false' para o método de localizar uma vez que estamos a utilizar um certificado de teste.


Segue-se código que utiliza o CertificateHelper e cria um ClientAssertionCertificate que é necessário para autenticação.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Eis o novo código para obter o token de acesso. Isto substitui o método de GetToken acima. Posso ter dado-um nome diferente para maior conveniência.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

Ter coloque todas este código para a classe de utilitários de meu projecto Web App para fácil utilização.

A última alteração de código está o método de Application_Start. Em primeiro lugar precisamos de chamar o método de GetCert() para carregar o ClientAssertionCertificate. E, em seguida, vamos alterar o método de chamada de retorno podemos fornecer ao criar um novo KeyVaultClient. Tenha em atenção que esta substitui o código que tivemos acima.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Adicionar um certificado para a sua aplicação Web através do Portal do Azure** Adicionar um certificado para a sua aplicação Web é um processo de dois passos simples. Em primeiro lugar, aceda ao Portal do Azure e navegue para a sua aplicação Web. No pá definições para a sua aplicação Web, clique em ligar a entrada para "Custom domains e SSL". Sobre o que é aberta pá poderão carregar o certificado que criou acima, KVWebApp.pfx, certifique-se de que não se esqueça a palavra-passe para o pfx.

![Adicionar um certificado para uma aplicação Web no Portal do Azure][2]


A última coisa que precisa de fazer consiste em Adicionar uma definição de aplicação para a sua aplicação Web que tem o Web site nome\_carga\_certificados e um valor de *. Isto irá garantir que todos os certificados são carregados. Se quiser carregar apenas os certificados que carregou, em seguida, pode introduzir uma lista dos seus miniaturas de separados por vírgulas.

Para saber mais sobre como adicionar um certificado para uma aplicação Web, consulte o artigo [Utilizar certificados nas aplicações de Web sites do Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)


**Adicionar um certificado para chave cofre como uma palavra-passe** Em vez de carregar o certificado diretamente para o serviço Web App, pode guardá-la na chave cofre como uma palavra-passe e implementá-lo a partir daí. Este é um processo de dois passos está descrito a seguinte mensagem de blogue, [Implementar Azure Web App certificado através de chave do cofre](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)



## <a id="next"></a>Próximos passos ##


Para programação referências, consulte o artigo [Azure chave cofre c# cliente API referência](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
