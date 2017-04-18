<properties
    pageTitle="Iniciar sessão sobreposição chave no Azure AD | Microsoft Azure"
    description="Este artigo aborda as assinatura sobreposição chave melhores práticas para o Azure Active Directory"
    services="active-directory"
    documentationCenter=".net"
    authors="gsacavdm"
    manager="krassk"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="gsacavdm"/>

# <a name="signing-key-rollover-in-azure-active-directory"></a>Iniciar sessão sobreposição chave no Azure Active Directory

Este tópico explica o que precisa de saber sobre as chaves públicas que são utilizadas no Azure Active Directory (Azure AD) para assinar tokens de segurança. É importante ter em atenção que estas teclas passagem com o rato numa base periódica e, no caso de emergência, poderá ser RETROCESSO imediatamente. Todas as aplicações que utilizam o Azure AD deverá conseguir através de programação lidar com o processo de chave passagem com o rato ou estabelecer um processo de sobreposição manual periódicos. Continue a ler para compreender como funcionam as teclas, como avaliar o impacto das sobreposição à aplicação e como atualizar a sua aplicação ou estabelecer um processo de sobreposição manual periódicos para processar sobreposição chave se for necessário.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Descrição geral de iniciar sessão teclas no Azure AD

Azure AD utiliza criptografia de chave pública criada com base em normas da indústria para estabelecer fidedignidade entre si próprio e as aplicações que utilização-la. Em termos práticos, isto funciona, da seguinte forma: Azure AD utiliza uma chave de assinatura é constituído por um par de chaves público e privado. Quando um utilizador inicia sessão para uma aplicação que utiliza o Azure AD para autenticação, Azure AD cria um token de segurança que contém informações sobre o utilizador. Este token está assinado pelo Azure AD utilizando a sua chave privada antes de ter sido enviada para a aplicação. Para verificar o token é válida e realmente originadas a partir do Azure AD, a aplicação tem de validar assinatura do token utilizando a chave pública exposta pela Azure AD está contida num [documento de deteção OpenID ligar](http://openid.net/specs/openid-connect-discovery-1_0.html) ou WS/SAML-Fed [documento de metadados de Federação](active-directory-federation-metadata.md)do inquilino.

Por motivos de segurança, Azure AD iniciar lançamentos chaves numa base periódica e, no caso de emergência podem ser apresentados ao longo do imediatamente. Qualquer aplicação que integra-se com o Azure AD deve ser preparada para processar um evento de sobreposição chave independentemente frequência podem ocorrer. Se não tiver e a aplicação tentativas para utilizar uma chave expirada para verificar a assinatura num token, o pedido de início de sessão no irá falhar.

Há sempre mais do que uma chave válida disponível no documento de deteção OpenID ligar e o documento de metadados de Federação. A aplicação deverá estar preparado para utilizar qualquer uma das chaves especificadas no documento, uma vez que uma tecla pode ser apresentada mais cedo, outro pode ser sua substituição e assim sucessivamente.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Como avaliar se a sua aplicação, será afectada e o que fazer acerca do mesmo

Como a aplicação trata sobreposição chave depende variáveis, como o tipo de aplicação ou que protocolo de identidade e biblioteca foi utilizado. As secções abaixo determinar se os tipos de aplicações mais comuns são afetados pela sobreposição de chave e fornecem orientações sobre como atualizar a aplicação para suportar sobreposição automática ou atualizar manualmente a chave.

* [Aplicações de cliente nativo aceder a recursos](#nativeclient)
* [Aplicações Web / APIs aceder a recursos](#webclient)
* [Aplicações Web APIs proteger recursos e construídas utilizando dos serviços de aplicação do Azure](#appservices)
* [Aplicações Web / APIs proteger recursos utilizando .NET OWIN OpenID ligar, WS Fed ou software WindowsAzureActiveDirectoryBearerAuthentication intermédio](#owin)
* [Aplicações Web / APIs proteger recursos utilizando o software intermédio .NET Core OpenID ligar-se ou JwtBearerAuthentication](#owincore)
* [Aplicações Web / APIs proteger recursos utilizar Node.js passport azure-ad módulo](#passport)
* [Aplicações Web APIs proteger recursos e criados com o Visual Studio 2015](#vs2015)
* [Aplicações Web proteger recursos e criados com o Visual Studio 2013](#vs2013)
* [APIs Web proteger recursos e criados com o Visual Studio 2013](#vs2013_webapi)
* [Aplicações Web proteger recursos e criados com o Visual Studio 2012http](#vs2012)
* [Aplicações Web proteger recursos e criados com o Visual Studio 2010http, 2008 the utilizando o Windows Foundation de identidade](#vs2010)
* [Aplicações Web / APIs proteger recursos utilizando quaisquer outras bibliotecas ou manualmente implementar qualquer um dos protocolos suportados](#other)

Estas orientações são **não** aplicável para:

* Aplicações adicionadas a partir do Azure AD aplicação galeria (incluindo personalizadas) tem orientações em separado no que diz respeito chaves de assinatura. [Obter mais informações.](active-directory-sso-certs.md)
* No local aplicações publicadas através do proxy de aplicação não tem de se preocupar com chaves de assinatura.

### <a name="nativeclient"></a>Aplicações de cliente nativo aceder a recursos

Aplicações que estão apenas a aceder a recursos (i. e Microsoft Graph, KeyVault, API do Outlook e outras APIs Microsoft) geralmente apenas obter um token e passam-lo ao longo de ao proprietário do recurso. Dado que estes não estão a proteger quaisquer recursos, se não inspecionar o token de e, consequentemente, não necessita de para se certificar de que está a ser assinado corretamente.

Aplicações de cliente nativo, quer seja de ambiente de trabalho ou telemóvel, abrange nesta categoria e assim que não são afectadas pela passagem com o rato.

### <a name="webclient"></a>Aplicações Web / APIs aceder a recursos

Aplicações que estão apenas a aceder a recursos (i. e Microsoft Graph, KeyVault, API do Outlook e outras APIs Microsoft) geralmente apenas obter um token e passam-lo ao longo de ao proprietário do recurso. Dado que estes não estão a proteger quaisquer recursos, se não inspecionar o token de e, consequentemente, não necessita de para se certificar de que está a ser assinado corretamente.

Aplicações Web e web APIs que estiver a utilizar o fluxo de só de aplicação (credenciais de cliente / certificado de cliente), abrange nesta categoria e, portanto, não são afetada pela passagem com o rato.

### <a name="appservices"></a>Aplicações Web APIs proteger recursos e construídas utilizando dos serviços de aplicação do Azure

Autenticação dos serviços aplicação Azure / funcionalidade autorização (EasyAuth) já tem a lógica necessária para processar automaticamente chave passagem com o rato.

### <a name="owin"></a>Aplicações Web / APIs proteger recursos utilizando .NET OWIN OpenID ligar, WS Fed ou software WindowsAzureActiveDirectoryBearerAuthentication intermédio

Se a sua aplicação está a utilizar o .NET OWIN OpenID ligar, WS Fed ou software de intermédio WindowsAzureActiveDirectoryBearerAuthentication, que já tem a lógica necessária para processar automaticamente chave passagem com o rato.

Pode confirmar que a aplicação está a utilizar qualquer um por está à procura de qualquer uma de fragmentos seguintes na sua aplicação Startup.cs ou Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
    });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Aplicações Web / APIs proteger recursos utilizando o software intermédio .NET Core OpenID ligar-se ou JwtBearerAuthentication

Se a sua aplicação está a utilizar o software de intermédio .NET Core OWIN OpenID ligar-se ou JwtBearerAuthentication, que já tem a lógica necessária para processar automaticamente chave passagem com o rato.

Pode confirmar que a aplicação está a utilizar qualquer um por está à procura de qualquer uma de fragmentos seguintes na sua aplicação Startup.cs ou Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
    });
```

### <a name="passport"></a>Aplicações Web / APIs proteger recursos utilizar Node.js passport azure-ad módulo

Se a sua aplicação está a utilizar o módulo de passport ad Node.js, que já tem a lógica necessária para processar automaticamente chave passagem com o rato.

Pode confirmar se o seu anúncio de passport aplicação ao procurar o fragmento que se segue na app.js sua aplicação

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Aplicações Web APIs proteger recursos e criados com o Visual Studio 2015

Se a aplicação foi criada utilizando um modelo de aplicação web no Visual Studio 2015 e **Trabalho e contas escolares** que selecionou a partir do menu de **Alteração de autenticação** , que já tem a lógica necessária para processar automaticamente chave passagem com o rato. Esta lógica, incorporada no software intermédio OWIN OpenID ligar, obtém e coloca em cache as teclas do documento de deteção OpenID ligar e periodicamente atualiza-los.

Se adicionou autenticação a sua solução manualmente, a aplicação poderá não ter a lógica de sobreposição de chave necessárias. Terá de escrever o seu próprio ou siga os passos no [aplicações Web / APIs utilizar outras bibliotecas ou manualmente implementar qualquer um dos protocolos suportados.](#other).

### <a name="vs2013"></a>Aplicações Web proteger recursos e criados com o Visual Studio 2013

Se a aplicação foi criada utilizando um modelo de aplicação web no Visual Studio 2013 e que selecionou **Contas organizacionais** a partir do menu de **Alteração de autenticação** , que já tem a lógica necessária para processar automaticamente chave passagem com o rato. Esta lógica armazena Identificador exclusivo da sua organização e as informações da chave assinatura em duas tabelas de base de dados associadas ao projeto. Pode encontrar a cadeia de ligação para a base de dados no ficheiro de Web. config do projeto.

Se adicionou autenticação a sua solução manualmente, a aplicação poderá não ter a lógica de sobreposição de chave necessárias. Terá de escrever o seu próprio ou siga os passos no [aplicações Web / APIs utilizar outras bibliotecas ou manualmente implementar qualquer um dos protocolos suportados.](#other).

Os passos seguintes irão ajudá-lo certifique-se de que a lógica está a funcionar corretamente na sua aplicação.

1. No Visual Studio 2013, abra a solução e, em seguida, clique no separador **Servidor Explorer** na janela do lado direito.
2. Expanda **as ligações de dados**, **DefaultConnection**e, em seguida, **tabelas**. Localizar a tabela **IssuingAuthorityKeys** , faça duplo clique nele e, em seguida, clique em **Mostrar dados de tabela**.
3. Na tabela **IssuingAuthorityKeys** , haverá pelo menos uma linha, que corresponde ao valor na chave impressão digital. Elimine as linhas na tabela.
4. Botão direito do rato na tabela de **inquilinos** e, em seguida, clique em **Mostrar dados de tabela**.
5. Na tabela **inquilinos** , haverá pelo menos uma linha, que corresponde a um identificador de inquilino do directório exclusivo. Elimine as linhas na tabela. Se não eliminar as linhas na tabela de **inquilinos** e **IssuingAuthorityKeys** tabela, obterá um erro o tempo de execução.
6. Criar e executar a aplicação. Depois de ter iniciado sua conta, pode parar a aplicação.
7. Voltar para o **Servidor Explorer** e observe os valores na tabela **IssuingAuthorityKeys** e **inquilinos** . Notará que tenham sido preenchidos automaticamente, com as informações adequadas do documento de metadados Federação.

### <a name="vs2013"></a>APIs Web proteger recursos e criados com o Visual Studio 2013

Se criou uma aplicação de API web no Visual Studio 2013 utilizando o modelo da Web API e, em seguida, selecionado **Contas organizacionais** a partir do menu de **Alteração de autenticação** , que já tem a lógica necessária na sua aplicação.

Se tiver configurado manualmente autenticação, siga as instruções abaixo para saber como configurar o seu API Web para atualizar automaticamente as suas informações da chave.

O fragmento de código seguinte demonstra como obter as teclas mais recentes do documento de metadados de Federação e, em seguida, utilize o [Processador de tokens de JWT](https://msdn.microsoft.com/library/dn205065.aspx) para validar o token. O fragmento de código assume que que irá utilizar seu próprio mecanismo colocação em cache para persistência a chave para validar os tokens futuros a partir do Azure AD, quer seja numa base de dados, ficheiro de configuração ou qualquer outro lugar.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Aplicações Web proteger recursos e criados com o Visual Studio 2012http

Se a aplicação foi criada no Visual Studio 2012, provavelmente utilizou a identidade e a ferramenta do Access para configurar a aplicação. Também é provável que está a utilizar a [Validação de registo de nome de emissor (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). O VINR é responsável por manter informações acerca dos fornecedores de identidades fidedigno (Azure AD) e as teclas utilizadas para validar os tokens emitidos por-los. O VINR também torna mais fácil atualizar automaticamente as informações da chave armazenadas num ficheiro Web. config transferindo o documento mais recente de metadados de Federação associado com o seu diretório, verificar se a configuração está desatualizada com o documento mais recente e atualizar a aplicação para utilizar a nova chave conforme necessário.

Se criou a sua aplicação utilizando qualquer um dos exemplos de código ou guiadas documentação fornecida pela Microsoft, a lógica de sobreposição chave já é incluída no seu projeto. Vai notar que já existe o código abaixo no projeto. Se a aplicação ainda não tem esta lógica, siga os passos abaixo para adicioná-la e para verificar que está a funcionar corretamente.

1. No **Explorador de soluções**, adicione uma referência para a assemblagem **System.IdentityModel** para o projeto adequado.
2. Abra o ficheiro **Global.asax.cs** e adicione o seguinte utilizando diretivas do:
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Adicione o seguinte método para o ficheiro **Global.asax.cs** :
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Invocar o método de **RefreshValidationSettings()** no método **Application_Start()** no **Global.asax.cs** conforme apresentado:
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Depois de ter seguido estes passos, ficheiro de Web. config a aplicação será atualizado com as informações mais recentes do documento de metadados do Federação, incluindo as teclas mais recentes. Esta atualização irá ocorrer sempre que o conjunto de aplicações Reciclagens no IIS; Por predefinição IIS está definido para reciclagem aplicações cada horas 29.

Siga os passos abaixo para verificar a lógica de sobreposição chave está a funcionar.

1. Depois de ter verificado que a aplicação está a utilizar o código acima, abra o ficheiro da **Web. config** e navegue para a **<issuerNameRegistry>** bloco, especificamente está à procura de seguintes algumas linhas:
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. No **<add thumbprint=””>** definir, alterar o valor de impressão digital substituindo qualquer caráter por uma diferente. Guarde o ficheiro da **Web. config** .

3. Criar a aplicação e, em seguida, executá-la. Se pode concluir o processo de início de sessão, a aplicação está com êxito a atualizar o a chave ao transferi as informações necessárias a partir do documento de metadados de Federação do seu diretório. Se estiver a ter problemas em iniciar sessão, certifique-se as alterações na sua aplicação estão corretas ao lendo o tópico [Adicionar Sign-On para o utilizar de aplicação Web Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) , ou transferir e da inspeção do código seguinte de exemplo: [Inquilino com várias nuvem aplicação para o Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).


### <a name="vs2010"></a>Aplicações Web proteger recursos e criados com o Visual Studio 2008 ou 2010 e v 1.0 de identidade de Windows Foundation (WIF) para .NET 3.5

Se uma aplicação num WIF 1.0 já criado, não existe nenhuma mecanismos fornecido para atualizar automaticamente a configuração da sua aplicação para utilizar uma nova chave.

- *Forma mais fácil* Utilize as ferramentas de FedUtil incluída no WIF SDK, que pode obter o documento mais recente de metadados e atualizar a sua configuração.
- Atualize a sua aplicação para 4,5 .NET, que inclui a versão mais recente do WIF localizada no espaço de nomes do sistema. Em seguida, pode utilizar a [Validação de registo de nome de emissor (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) para executar as atualizações automáticas da configuração da aplicação.
- Execute uma sobreposição manual de acordo com as instruções no final deste documento orientações.

Instruções para utilizar o FedUtil para atualizar a sua configuração:

1. Certifique-se de que tem o v 1.0 WIF SDK instalado no seu computador de desenvolvimento para Visual Studio 2008 ou 2010. Pode [transferi-lo a partir daqui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se que ainda não tiver instalado-lo.
2. No Visual Studio, abra a solução e, em seguida, com o botão direito do projecto aplicável e selecione **Atualizar Metadados de Federação**. Se esta opção não estiver disponível, FedUtil e/ou a v 1.0 WIF SDK não foi instalado.
3. Linha de comandos, selecione **Atualizar** para começar a atualizar os seus metadados de Federação. Se tiver acesso ao ambiente de servidor onde a aplicação está alojada, opcionalmente, pode utilizar do FedUtil [metadados automática atualizar programador](https://msdn.microsoft.com/library/ee517272.aspx).
4. Clique em **Concluir** para concluir o processo de atualização.

### <a name="other"></a>Aplicações Web / APIs proteger recursos utilizando quaisquer outras bibliotecas ou manualmente implementar qualquer um dos protocolos suportados

Se estiver a utilizar alguns outra biblioteca ou manualmente implementada qualquer um dos protocolos suportados, terá de rever a biblioteca ou a sua implementação para se certificar de que a tecla está a ser obtida a partir do documento de deteção OpenID ligar ou o documento de metadados de Federação. Uma forma de verificar a existência de isto é fazer uma pesquisa no seu código ou a biblioteca para qualquer chamadas de saída para o documento de deteção de OpenID ou o documento de metadados de Federação.

Se chave que está a ser armazenado num local ou na sua aplicação, pode manualmente obter a chave e atualização-lo em conformidade com a executar uma sobreposição manual de acordo com as instruções no final deste documento orientações. **É aconselhável que melhorar a sua aplicação para suportar a sobreposição automática** utilizando qualquer uma das abordagens destacar neste artigo para evitar interrupções futuras e sobrecarga se Azure AD aumenta é cadence passagem com o rato ou tem uma sobreposição de fora da banda emergência.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Como teste a sua aplicação para determinar se, será afetado

Pode validar se a aplicação suporta sobreposição de tecla automática ao transferir os scripts e seguir as instruções no artigo [este repositório GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Como efetuar uma sobreposição manual se de que a aplicação não suporta sobreposição automática

Se a aplicação é que **não** suporte automática sobreposição, terá de estabelecer um processo que periodicamente monitoriza chaves de assinatura do Azure AD e executa uma sobreposição manual em conformidade. [Repositório de GitHub este](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contém scripts e obter instruções sobre como o fazer.
