<properties
    pageTitle="Como instalar o conector de Proxy de aplicação AD Azure silenciosamente | Microsoft Azure"
    description="Aborda como executar uma instalação silenciosa do Azure AD aplicação Proxy conexão para fornecer acesso remoto seguro as suas aplicações no local."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Como instalar o conector de Proxy de aplicação do AD Azure silenciosamente

Pretende possam enviar um script de instalação para vários servidores do Windows ou para servidores do Windows que não têm a interface de utilizador ativada. Este tópico explica como criar um script do Windows PowerShell, que permite instalação automática instalar e registar a conexão de Proxy de aplicação AD Azure.

## <a name="enabling-access"></a>Ativar o acesso
Proxy de aplicação funciona instalando um serviço Windows Server slim denominado a conexão dentro da sua rede. Para o conector de Proxy de aplicação trabalhar-tem de estar registado junto de diretório da sua Azure AD utilizando um administrador global e a palavra-passe. Normalmente isto é introduzido durante a instalação de conexão num pop-up de caixa de diálogo. Em alternativa, pode utilizar o Windows PowerShell para criar um objeto de credenciais para introduzir as informações de registo, ou pode criar o seu próprio token e utilizá-la para introduzir as informações de registo.

## <a name="step-1--install-the-connector-without-registration"></a>Passo 1: Instalar o conector sem registo


Instale o conector MSIs sem registar o conector da seguinte forma:


1. Abra uma linha de comandos.
2. Execute o seguinte comando na qual o /q significa sossegada, instalação - a instalação não irá pedir para aceitar o contrato de licença do utilizador final.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Passo 2: Registar o conector com o Azure Active Directory
Isto pode ser feito utilizando um dos seguintes métodos:


- Registe-se a conexão utilizando um objeto de credenciais do Windows PowerShell
- Registe-se a conexão utilizando um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registe-se a conexão utilizando um objeto de credenciais do Windows PowerShell


1. Criar o objeto de credenciais do Windows PowerShell, executando o seguinte, onde "<username>"e"<password>" deve ser substituída com o nome de utilizador e palavra-passe para o seu diretório:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Aceda ao **Conector de Proxy de aplicação do C:\Program Files\Microsoft AAD** e executar o script utilizando o objeto de credenciais de PowerShell que criou, onde $cred é o nome do PowerShell credenciais de objeto que criou:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### <a name="register-the-connector-using-a-token-created-offline"></a>Registe-se a conexão utilizando um token criado offline

1. Crie um token offline utilizando a classe de AuthenticationContext utilizando os valores de fragmento de código:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Assim que tiver o token de criar um SecureString utilizando o token: <br>
`$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Execute o seguinte comando Windows PowerShell, onde SecureToken é o nome do token criado acima e tenantID é GUID do seu inquilino: <br>
`RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## <a name="see-also"></a>Consulte também

- [Ativar o Proxy de aplicação para o Azure Active Directory](active-directory-application-proxy-enable.md)
- [Publicar aplicações utilizando o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Resolução de problemas que ocorram com Proxy de aplicação](active-directory-application-proxy-troubleshoot.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)
